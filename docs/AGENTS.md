# Repository Guidelines

## Project Structure & Module Organization
This workspace follows standard Liferay layout: `modules/` holds OSGi apps (grouped by feature folders you create), `themes/` stores JS or classic themes, `configs/<env>/` contains environment-specific `.config` overrides, and `bundles/` is the assembled runtime dropped by `initBundle`. Shared docs live in `docs/`, while build logic stays in `build.gradle`, `gradle/`, and `gradle.properties`. Place integration assets (fixtures, sample data, screenshots) beside the module that owns them, and mirror bundle names when creating new directories to keep navigation predictable.

## Build, Test, and Development Commands
Run `./gradlew help` once after cloning to warm the Gradle cache. Use `./gradlew build` for a full compile plus unit tests, and `./gradlew deploy` to copy artifacts into `bundles/osgi/modules`. During server workflows, `blade gw initBundle` downloads the target bundle, `blade gw deploy` publishes local modules, and `blade server run` boots Tomcat with hot deploy enabled. For distribution tasks, `blade gw distBundleTar|Zip` and `blade gw buildDockerImage` (see `GETTING_STARTED.markdown`).

When you need a standalone portal, start it with `blade server start`, and switch to debug mode with `blade server start -d`. Keep the same terminal session open so you can stop the server cleanly and reuse the warmed Gradle/cache state.

## Module Scaffolding
Use Blade CLI to scaffold new modules so their structure matches upstream expectations. Run `blade create -l` to list all available templates (e.g., `api`, `mvc-portlet`, `service-builder`, `fragment`, `js-theme`) and pick the one that aligns with the feature. Example: `blade create -t service -p com.ejemplo.modulo.service ejemplo-modulo-service`.

Group related artifacts inside a dedicated folder under `modules/` (for example, `modules/custom-blog/custom-blog-api`, `custom-blog-service`, `custom-blog-web`) so APIs, services, and web layers stay organized by feature. Keep each service module’s `service.xml` authoritative—define entities, columns, indices, and custom finders there so Service Builder can regenerate the persistence layer and SQL safely.

Bookmark the official docs at https://learn.liferay.com and lean on them whenever you need deeper context about Blade templates, Service Builder, REST Builder, or ADT APIs beyond what this guide covers.

## Portal Runtime & ADT Templates
Application Display Templates (.ftl) should be created from scratch per portal and rely exclusively on the CSS already shipped by the corresponding custom theme. Avoid embedding extra styles in the ADT whenever the theme can supply them.

Centralize helper logic in `TEMPLATE_FUNCTIONS.ftl` instead of inlining repetitive `tf.getFieldValue(...)` usages across ADTs. For example:

```ftl
<#-- Devuelve el valor del campo 'fieldRef' (field-reference) de la estructura del 'entry' -->
<#function getFieldValue entry fieldRef parentRef=ti.BLANK defaultValue=ti.BLANK>
    <#local article = entry.getAssetRenderer().getArticle() />
    <#local document = saxReaderUtil.read(article.getContentByLocale(locale)) />
    <#local root = document.getRootElement() />
    <#local defaultLangId = article.getDefaultLanguageId()?string />

    <#if parentRef?has_content>
        <#local xpath = "//dynamic-element[@field-reference='${parentRef}']/dynamic-element[@field-reference='${fieldRef}']/dynamic-content" />
    <#else>
        <#local xpath = "//dynamic-element[@field-reference='${fieldRef}']/dynamic-content" />
    </#if>

    <#-- Primero idioma actual -->
    <#local node = root.selectSingleNode("${xpath}[@language-id='${locale}']")! />
    <#-- Fallback a idioma por defecto -->
    <#if !node?has_content>
        <#local node = root.selectSingleNode("${xpath}[@language-id='${defaultLangId}']")! />
    </#if>
    <#-- Último recurso: cualquier dynamic-content -->
    <#if !node?has_content>
        <#local node = root.selectSingleNode(xpath)! />
    </#if>

    <#return (node?has_content && node.getText()?has_content)?then(node.getText()?trim, defaultValue) />
</#function>

<#-- Devuelve un json con los atributos de la imagen del campo 'fieldRef' -->
<#function getFieldValueImage entry fieldRef parentRef=ti.BLANK>
    <#local raw = getFieldValue(entry, fieldRef, parentRef) />
    <#return getValueData(raw) />
</#function>
```

Whenever an ADT or `TEMPLATE_FUNCTIONS.ftl` needs access to services, initialize them once inside `TEMPLATE_INIT.ftl` so every template shares the same service locator setup and debug flags:

```ftl
<#assign TEMPLATE_INIT_KEY = 33369 />
<#assign TEMPLATE_FUNCTIONS_KEY = 33323 />

<#assign BLANK = "" />
<#assign ADMIN_PERMISSION = permissionChecker.isOmniadmin() />
<#assign DEBUG_ALWAYS = true /> <#-- Fuerza el modo debug para todos los roles -->

<#assign AssetEntryLocalService = serviceLocator.findService("com.liferay.asset.kernel.service.AssetEntryLocalService")>
<#assign JournalArticleLocalService = serviceLocator.findService("com.liferay.journal.service.JournalArticleLocalService")>
<#assign AssetCategoryLocalService = serviceLocator.findService("com.liferay.asset.kernel.service.AssetCategoryLocalService")>
<#assign LayoutLocalService = serviceLocator.findService("com.liferay.portal.kernel.service.LayoutLocalService")>
<#assign GroupLocalService = serviceLocator.findService("com.liferay.portal.kernel.service.GroupLocalService")>
<#assign DLAppService = serviceLocator.findService("com.liferay.document.library.kernel.service.DLAppService")>
<#assign DLUtil = staticUtil["com.liferay.document.library.kernel.util.DLUtil"]>
<#assign HttpComponentsUtil = staticUtil["com.liferay.portal.kernel.util.HttpComponentsUtil"]>
```

Import `TEMPLATE_INIT.ftl` first in every ADT, then load `TEMPLATE_FUNCTIONS.ftl` so the helper methods and services are always ready before rendering.

## Coding Style & Naming Conventions
Java follows Liferay defaults: 4-space indentation, `PascalCase` classes, `camelCase` members, package roots such as `com.liferay.workspace.<feature>`. Use `kebab-case` directory names under `modules/` and suffix implementations with their pattern (e.g., `*-api`, `*-service`, `*-web`). Front-end code prefers 2-space indents and strict ESLint/Prettier defaults. Run `./gradlew formatSource` (or `blade gw formatSource`) before sending a review; it applies Liferay Source Formatter to Java, JSP, and JS.

Liferay uses Bootstrap 4.X.X as the native CSS framework. When build themes or custom .css/.scss files, use Bootstrap utility classes where possible instead of inventing new styl.es

## Testing Guidelines
Unit tests live next to source under `src/test/java` and use JUnit (Gradle’s `test` task). Integration tests belong in `src/testIntegration/java` and run via `./gradlew :modules:example:example-test:testIntegration`. Name classes `<Feature>Test` or `<Feature>IntegrationTest`, and keep fixtures under `src/test/resources`. Aim for meaningful coverage on service and REST layers; document gaps in the PR if a module lacks automated tests.

## Commit & Pull Request Guidelines
Existing history uses short imperative subjects (`Setup Liferay 7.4 ga132`). Continue that style, optionally prefixed with a scope (`modules:`, `themes:`) and link issues (`LRQA-123`). Each PR should describe the problem, solution, test evidence, and any configuration steps. Include screenshots or terminal snippets when a UI or server behavior changes, and verify `./gradlew build` before requesting review.

## Security & Configuration Tips
Store secrets in `gradle-local.properties` or environment variables, never in Git. Use `configs/local` for developer overrides and `configs/prod` for locked-down values. When exposing ports or OSGi configs, double-check `platform.bndrun` and `configs/docker` so that credentials are masked before sharing artifacts.
