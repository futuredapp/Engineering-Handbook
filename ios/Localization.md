# Localization

We take two different approaches when localizing iOS apps. Primarily, we use POEditor with Fastlane plugin
to synchronize it by calling one command. Alternative approach is to use ACKLocalization tool synchronized
with a Google sheet. Optionally, we use R.swift to check that localization resources are synchronized at compile time.

## Primary approach

1. First, we need to create POEditor project. Usually, we have one project containing both Android and iOS strings.
2. If we use [imported Fastlane configuration](https://github.com/futuredapp/fastlane) we need to setup POEditor
   environment variables. The imported lanes use
   [Poesie + Fastlane plugin](https://github.com/Patrez/fastlane-plugin-poesie) by our colleague
   [Patrik Potoček](https://github.com/Patrez). You can easily use this plugin in custom Fastlane configurations.
3. Optionally, setup R.swift.
4. Call your localization lane (`localize` when using imported lanes) for updating your string files.

## Alternative approach

Using Google Sheets as an alternative approach has a few minor pros and cons:

+ Pros:
  + No need to pay for POEditor.
  + Versioning.
- Cons:
  - Sheet can be broken when the user is not careful (but the changes are reversible).

Setup using the guide provided in [ACKLocalization](https://github.com/AckeeCZ/ACKLocalization).

## Generating resources

We have a good experience with creating framework with all the resource files and generating public headers for it
using [R.swift](https://github.com/mac-cain13/R.swift). This makes the whole process resilient to changes.
