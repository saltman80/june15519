```markdown
# june15519 - AppSumo Product Title Scraper

> A Chrome Extension designed to help users scrape product titles from the AppSumo website, save them, and export the collected list as a CSV file.

## Overview

june15519 is a Chrome Extension built to streamline the process of collecting product titles from AppSumo. If you're conducting product research or just want to keep a list of interesting deals, this extension allows you to easily click on product titles, store them, and then export your collection for offline analysis.

The extension utilizes Manifest V3 and is designed for ease of use, with options for customizing how product titles are identified on AppSumo pages.

## Features

*   **One-Click Title Saving:** Click on product titles on AppSumo pages to save them instantly.
*   **Persistent Storage:** Collected titles are stored within your browser.
*   **Popup Interface:**
    *   View a list of all saved titles.
    *   Export the collected list to a CSV file.
    *   Clear all saved titles with a single click.
*   **CSV Export:** Download your list of product titles in CSV format for use in spreadsheets or other analysis tools.
*   **Configurable Selectors:** An Options Page allows you to define and save custom CSS selectors for identifying product titles, making the extension adaptable to changes in AppSumo's website structure.
*   **Visual Feedback:** Receive clear visual confirmation when a title is successfully saved.
*   **Duplicate Handling:** Automatically prevents duplicate titles from being added to your list by default.

## Architecture

The AppSumo Product Title Scraper follows a standard Chrome Extension architecture (Manifest V3):

1.  **Content Script (`appsumocontentscript.js` & `contentpagestyles.css`):**
    *   Injects into AppSumo web pages.
    *   Retrieves configured CSS selectors (from Options Page via `chrome.storage`).
    *   Identifies product titles using these selectors (potentially with fallback logic from `refinedcontentscriptlogicfordynamicselectors.js`).
    *   Modifies the DOM to make titles selectable (e.g., adding click listeners).
    *   Provides user feedback on save (styled by `contentpagestyles.css`).
    *   Sends selected titles to the Background Script.
2.  **Background Script (Service Worker - `appsumobackgroundworker.js`):**
    *   Acts as the central controller and data manager.
    *   Listens for messages from the Content Script (to save titles) and the Popup (to retrieve titles, trigger export, clear data).
    *   Manages storing and retrieving titles using `chrome.storage.local` with help from `storagemanagementutility.js`.
    *   Handles CSV generation and download initiation using `chrome.downloads` API, assisted by `csvexportutility.js`.
3.  **Popup (`extensionpopupmarkup.html`, `extensionpopupinterface.js`, `extensionpopupstyles.css`):**
    *   Provides the user interface via the extension icon.
    *   Displays saved titles.
    *   Allows users to trigger CSV export and clear the list.
    *   May link to the Options Page.
    *   Layout: `extensionpopupmarkup.html`, Interactivity: `extensionpopupinterface.js`, Styling: `extensionpopupstyles.css`.
4.  **Options Page (`options/options.html`, `options/options.js`, `options/options.css`):**
    *   Allows users to configure extension settings, primarily CSS selectors for product titles.
    *   Settings are saved using `chrome.storage.sync` or `chrome.storage.local`.
    *   Structure: `options/options.html`, Logic: `options/options.js`, Styling: `options/options.css`.

## Project Structure

The project includes the following key files:

*   **`manifest.json`**: Core configuration file for the Chrome Extension. Defines metadata, permissions, scripts, and actions.
*   **`appsumocontentscript.js`**: JavaScript file injected into AppSumo pages to handle title identification and interaction.
*   **`contentpagestyles.css`**: CSS file to style elements or feedback provided by the content script on AppSumo pages.
*   **`appsumobackgroundworker.js`**: Service worker that manages background tasks, data storage, and communication between components.
*   **`storagemanagementutility.js`**: Utility script with functions for managing the storage of product titles (saving, retrieving, clearing, duplicate checking).
*   **`csvexportutility.js`**: Utility script with functions for generating CSV content from the list of titles.
*   **`refinedcontentscriptlogicfordynamicselectors.js`**: Contains specialized logic for handling dynamic or evolving CSS selectors or suggesting selectors.
*   **`extensionpopupmarkup.html`**: HTML structure for the extension's popup interface.
*   **`extensionpopupinterface.js`**: JavaScript file for handling the logic and interactivity of the popup.
*   **`extensionpopupstyles.css`**: CSS file for styling the extension's popup.
*   **`options/options.html`**: HTML structure for the extension's options page.
*   **`options/options.js`**: JavaScript file for handling the logic of the options page (saving/loading settings).
*   **`options/options.css`**: CSS file for styling the options page.
*   **`icons/`**: Directory containing extension icons:
    *   `icon16.png`
    *   `icon48.png`
    *   `icon128.png`
*   **`projectdocumentation.md`**: Additional project documentation (as referenced in project plan).

## Installation

To install and use this extension in developer mode:

1.  **Download or Clone the Repository:**
    *   If you have a ZIP file, extract it to a local folder.
    *   If you have access to the Git repository, clone it:
        ```bash
        git clone <repository-url> june15519
        cd june15519
        ```
2.  **Open Chrome Extensions Page:**
    *   Open Google Chrome.
    *   Navigate to `chrome://extensions`.
3.  **Enable Developer Mode:**
    *   In the top right corner of the Extensions page, toggle the "Developer mode" switch to ON.
4.  **Load Unpacked Extension:**
    *   Click the "Load unpacked" button that appears.
    *   Navigate to the directory where you downloaded or cloned the project (e.g., the `june15519` folder).
    *   Select the folder.
5.  The "AppSumo Product Title Scraper" extension should now appear in your list of extensions and be active. You will see its icon in the Chrome toolbar.

## How to Use

1.  **Configuration (Optional):**
    *   Right-click the extension icon in the Chrome toolbar and select "Options" (or access it via a link in the popup if available).
    *   On the Options Page, you can review or modify the CSS selector used to identify product titles on AppSumo. This is useful if AppSumo changes its website structure.
    *   Save any changes. The extension will use these selectors (or default ones if none are set).

2.  **Navigate to AppSumo:**
    *   Go to any AppSumo product listing page (e.g., browse, categories, search results).

3.  **Collect Product Titles:**
    *   The content script (`appsumocontentscript.js`) will automatically identify product titles based on the configured (or default) CSS selectors.
    *   Click on a product title (or an associated save icon if implemented).
    *   You should see a visual confirmation (e.g., "Title Saved!").
    *   The title is then sent to the background script and saved.

4.  **View, Export, or Clear Titles (Using the Popup):**
    *   Click the extension icon in the Chrome toolbar to open the popup.
    *   **View Titles:** The popup will display a list of all product titles you've saved.
    *   **Export to CSV:** Click the "Export to CSV" button. A CSV file containing all your saved titles will be generated and downloaded.
    *   **Clear All Titles:** Click the "Clear All Titles" button to remove all saved titles from the extension's storage. You'll likely be asked for confirmation.

## Configuration

The extension allows for configuration of the CSS selectors used to find product titles on AppSumo pages. This is crucial for maintaining functionality if AppSumo updates its website's HTML structure.

*   **Accessing Options:** Right-click the extension icon and select "Options," or use a link from the popup.
*   **Setting Selectors:** In the `options/options.html` page, you can input and save the CSS selector(s). `options.js` handles saving these to `chrome.storage`.
*   **Fallback Logic:** The `refinedcontentscriptlogicfordynamicselectors.js` may provide fallback mechanisms or advanced detection if user-configured selectors fail or are not set.

## `manifest.json` Example Snippet

The `manifest.json` file is central to the extension. Here's an example structure:

```json
{
  "manifest_version": 3,
  "name": "june15519 - AppSumo Product Title Scraper",
  "version": "1.0.0",
  "description": "Scrapes product titles from AppSumo and exports them to CSV.",
  "permissions": [
    "storage",      // To store titles and settings
    "activeTab",    // To interact with the current AppSumo tab (alternative to specific host permissions for content scripts)
    "scripting",    // Required for programmatically injecting content scripts with activeTab
    "downloads"     // To initiate CSV file downloads
  ],
  "host_permissions": [
    "*://*.appsumo.com/*" // Allows the extension to run on AppSumo pages
  ],
  "background": {
    "service_worker": "appsumobackgroundworker.js"
  },
  "content_scripts": [
    {
      "matches": ["*://*.appsumo.com/*"], // Adjust match patterns as needed
      "js": ["appsumocontentscript.js", "refinedcontentscriptlogicfordynamicselectors.js"],
      "css": ["contentpagestyles.css"]
    }
  ],
  "action": {
    "default_popup": "extensionpopupmarkup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  },
  "options_page": "options/options.html"
}
```
**Note:** The `host_permissions` grant broad access to AppSumo. The `content_scripts` `matches` array should be refined to target specific AppSumo pages where product titles appear (e.g., browse, product pages, search results).

## Dependencies

This is a self-contained Chrome Extension. Its primary dependencies are the Chrome Extension APIs provided by the browser, such as:

*   `chrome.storage` (for local and sync storage)
*   `chrome.scripting` (for content script injection and execution)
*   `chrome.downloads` (for file downloads)
*   `chrome.runtime` (for messaging and extension lifecycle events)
*   `chrome.action` (for popup and toolbar icon interaction)

No external JavaScript libraries are planned for the core functionality as per the project description.

## Key Considerations

*   **Error Handling:** Robust error handling is crucial across all components (DOM manipulation, storage operations, message passing, CSV export).
*   **Duplicate Handling:** `storagemanagementutility.js` is designed to prevent duplicate titles from being saved.
*   **User Feedback:** Clear and consistent feedback is provided for actions like saving a title or errors.
*   **Code Quality:** Adherence to JavaScript best practices, potential linting, and formatting are recommended.
*   **Documentation:** Refer to `projectdocumentation.md` for more in-depth technical details.

## Future Considerations

While the current scope focuses on title scraping, potential future enhancements include:

*   **Internationalization (`_locales`):** Support for multiple languages.
*   **Advanced Error Reporting:** More sophisticated ways to log or report errors.
*   **Scraping Additional Fields:** Ability to scrape other data points like price, product URL, etc.
*   **Cloud Sync for Titles:** Option to sync saved titles across devices (would require `chrome.storage.sync` for titles and careful quota management).
*   **UI/UX Improvements:** Enhanced styling, animations, or user experience refinements.

## Contributing

Contributions are welcome! If you'd like to contribute, please follow these general steps:

1.  Fork the repository.
2.  Create a new branch for your feature or bug fix: `git checkout -b feature/your-feature-name` or `bugfix/issue-number`.
3.  Make your changes and commit them with clear, descriptive messages.
4.  Push your changes to your forked repository.
5.  Open a Pull Request to the main repository, detailing your changes.

Please ensure your code adheres to the existing style and that any new features are well-tested.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details (if one is created for the project).
```