# iA Writer Templates

Preview, create PDFs, and print documents in your own style with templates in iA Writer.

Templates are built with web pages. You can use HTML, CSS, and JavaScript to lay out your documents. If you know how to make a web site, you can easily make an iA Writer template.

iA Writer templates are [bundles](https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/Bundle.html): directories that group template files together. Bundles are natively supported by macOS and iOS. To browse template contents, right-click it in Finder and choose “Show Bundle Contents”.

```
Example.iatemplate/
    Contents/
        Info.plist
        Resources/
            document.html
            title.html
            header.html
            footer.html
            style.css
```

### Installing Templates

##### macOS

- Double-click a template in Finder
- Drag to iA Writer icon in Dock
- Add in Preferences → Templates

Note: Template bundles are copied when installed. Any changes made to the original bundle will not be visible in iA Writer. You may modify the installed bundles. To find an installed bundle, right-click one in Preferences and select “Show in Finder”.

##### iOS

iOS supports adding template bundles, as well as ZIP archives, with one template per archive.

- Send via AirDrop
- “Copy to iA Writer” from Safari, Mail, iCloud Drive, or another sync service

##### Windows (v. 1.2+)

Download the template then, from iA Writer's menu choose: File → Install Template → Select 'TemplateName.iatemplate.zip' file. 

### HTML Files

iA Writer templates use separate HTML files for laying out the title page, document text, headers, and footers.

### Template Properties

Template pages and other properties are defined in `Info.plist`. We recommend starting with our example bundle if you’ve never dealt with property list files before.  XML property list files can be edited in any text editor.

<img src="Info.png?raw=true">

| Key | Value | Presence | Description
|:--|:--|:--|:--|
| `CFBundleName` | String | Required | Template name shown in iA Writer. |
| `CFBundleIdentifier` | String | Required | Uniquely identifies<sup>1</sup> a template. |
| `IATemplateDocumentFile` | String<sup>2</sup> | Required | Lays out document text. |
| `IATemplateTitleFile` | String<sup>2</sup> | Optional | Lays out a title page on export. |
| `IATemplateHeaderFile` | String<sup>2</sup> | Optional | Draws at the top of each document page on export. |
| `IATemplateFooterFile` | String<sup>2</sup> | Optional | Draws at the bottom of each document page on export. |
| `IATemplateHeaderHeight` | Number | Optional | Header height, in CSS points (≤400). |
| `IATemplateFooterHeight` | Number | Optional | Footer height, in CSS points (≤400). |
| `IATemplateDescription` | String | Recommended | A sentence that describes your template. |
| `IATemplateAuthor` | String | Recommended | A person (or a company) who created the template. |
| `IATemplateAuthorURL` | String | Recommended | A link to template’s author. |
| `IATemplateSuportsSmartTables` | Boolean | Optional | Whether to process Smart Tables. Defaults to YES if absent. |
| `IATemplateSupportsMath` | Boolean | Optional | Whether to process TeX math expressions into MathML. Defaults to YES if absent. |
| `IATemplateTitleUsesHeaderAndFooterHeight` | Boolean | Optional | When set to NO changes header and footer height on title page to 0. Defaults to YES if absent. |

1. iA Writer uses the bundle identifier to associate templates with documents. Each template must have a unique identifier. The identifier must contain only alphanumeric (A-Z, a-z, 0-9), hyphen (-), and period (.) characters. The string should be in reverse-DNS format. For example, if your company’s domain is `example.com` you could assign the string `com.example.template` as the bundle identifier.
2. HTML file name, without path extension. The actual files themselves must have an `html` extension.



### Content

iA Writer applies content to HTML using JavaScript. It replaces `innerHTML` on elements with several predefined `data` attributes. 

For example, `<span data-date></span>` becomes `<span data-date>June 22, 2016</span>` when a page is loaded for export.

iA Writer dispatches an `ia-writer-change` event to elements when it updates them with new content.

<img src="Pages.png?raw=true">

#### Document

The document page lays out the text from the editor in Preview, printed documents, and PDFs.

| Attribute | Description |
|:--|:--|
| `data-document` | Document text as HTML. |

iA Writer uses [MultiMarkdown][MMD] (with a few additions) to convert Markdown to HTML. [Markup.txt](Markup.txt) contains some of the most commonly used markup in iA Writer. [Markup.html](Markup.html) is the resulting HTML for `data-document`.

[MMD]: https://github.com/fletcher/MultiMarkdown-5

#### Title Page, Header and Footer

Title page, header, and footer are used only in in printed documents and PDFs.

| Attribute | Description |
|:--|:--|
| `data-page-count` | Total page count of the exported document. |
| `data-page-number` | Current page number.<sup>1</sup> |

1. `data-page-number` is not available in title pages. They do not have a page number to keep numbering the same whether or not you include the title page during export.

[Date Formats]: http://www.unicode.org/reports/tr35/tr35-31/tr35-dates.html#Date_Format_Patterns
[Locale Adjustments]: https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSDateFormatter_Class/#//apple_ref/occ/clm/NSDateFormatter/dateFormatFromTemplate:options:locale:

#### All Pages

| Attribute | Description |
|:--|:--|
| `data-title` | Document title, taken from document file name. |
| `data-author` | Document author, if specified in Preferences. |
| `data-date` | Current date. You can specify date format<sup>1</sup> in the attribute value: `data-date="MMMM yyyy"`. Date format patterns are described in [Unicode Technical Standard #35][Date Formats]. On Windows, [Microsoft standards](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-date-and-time-format-strings) are used. |

1. `data-date` output may not contain exactly those components given in the attribute value, but may—for example—have locale-specific [adjustments][Locale Adjustments] applied: `yMMMMd` may become `MMMM d, y` in the US, and `d MMMM y` in the UK.

### Preview

We recommend all template authors spend a few minutes to make their templates look great in Preview by adding support for night mode, as well as system-wide font size on iOS.

iA Writer uses the `class` attribute of the `<html>` element for environment data. Avoid adding custom classes to this element, because they will be replaced when a template is loaded.

| Class | Description |
|:--|:--|
| `night-mode` | Night mode is active.<sup>1</sup> |
| `ios` | Preview on iOS.<sup>2</sup> |
| `mac` | Preview on macOS. |

1. Note that users may invert night mode in Preferences on macOS.
2. We strongly recommend using standard media queries to make your templates responsive. However, sometimes it’s important to differentiate between iOS and macOS. For example, default templates have a slightly different color scheme on each platform.

#### Developing Templates

To fully reload the currently visible template in Preview on macOS, press <kbd>Shift</kbd> <kbd>Command</kbd> <kbd>R</kbd>.

To enable Web Inspector in Preview, paste the following command into Terminal.

```
defaults write pro.writer.mac WebKitDeveloperExtras -bool true
```

On Windows, you can enable the Chromium inspector by using <kbd>Ctrl</kbd> <kbd>J</kbd>.

#### Vertical Margins

Avoid vertical margins and padding for the document page.

- iA Writer will adjust the `<html>` element padding in Preview to match Editor
- Top and bottom margins for printing and PDF export are instead controlled by header and footer height in `Info.plist`

#### Toolbar Color

iA Writer adjusts Preview toolbar colors to match the currently visible template on macOS. Be sure to set `color` and `background-color` on the `<html>` element.

#### Font Size on iOS

iA Writer supports system-wide font size on iOS. Users can adjust the font size in Settings > Display & Brightness > Font Size. Most templates should adjust font size for this setting.

| Class | Description |
|:--|:--|
| `content-size-xs` | An extra small font. |
| `content-size-s` | A small font. |
| `content-size-m` | A medium-sized font. |
| `content-size-l` | Default value. A large font. |
| `content-size-xl` | An extra large font. |
| `content-size-xxl` | An increasingly large font. |
| `content-size-xxxl` | The largest font option. |

Note that some users may enable accessibility sizes in Settings > General > Accessibility > Larger Text.

| Class |
|:--|
| `content-size-accessibility-m` |
| `content-size-accessibility-l` |
| `content-size-accessibility-xl` |
| `content-size-accessibility-xxl` |
| `content-size-accessibility-xxxl` |
