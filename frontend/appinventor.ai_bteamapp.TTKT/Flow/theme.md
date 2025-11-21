
# Automatic Event Theming for Blogger

## Overview

This script dynamically changes your Blogger website's background and logo based on predefined events such as holidays, promotions, or seasons. The entire configuration is managed through a single external `theme.json` file, allowing you to schedule and update themes without ever touching your Blogger template code again.

The script is specifically designed to create a "boxed layout" effect for event themes, where the background is applied to the empty spaces on the left and right of the main content area.

## Features

-   **Dynamic Theming:** Automatically applies a custom logo and background based on the current date.
-   **External Configuration:** All events are managed in a simple `theme.json` file, hosted externally.
-   **One-Time Events:** Set themes for specific date ranges (e.g., a product launch from 2025-08-15 to 2025-08-20).
-   **Recurring Annual Events:** Set themes for holidays that occur every year (e.g., Christmas) by specifying only the month and day.
-   **Cross-Year Event Handling:** Intelligently handles recurring events that span across the new year (e.g., from December 20th to January 5th).
-   **Default Fallback:** Automatically reverts to a default theme when no event is active.
-   **Boxed Layout:** Creates a visually appealing boxed layout, showing the event background in the side margins of the page.

## Prerequisites

-   This script is tailored for the **Plus UI Blogger theme**. The CSS selectors may need to be adjusted for other themes.

## Installation Guide

Follow these three steps to set up the event theming system on your blog.

### Step 1: Create the `theme.json` File

Create a file named `theme.json` and paste the following structure into it. This is where you will define all your events.

```json
{
  "events": [
    {
      "id": "new-year",
      "name": "New Year Celebration",
      "recurring": true,
      "startDate": "12-28",
      "endDate": "01-05",
      "assets": {
        "logoUrl": "https://example.com/logos/new-year-logo.png",
        "backgroundUrl": "https://example.com/backgrounds/new-year-bg.jpg"
      }
    },
    {
      "id": "product-launch-2025",
      "name": "Special Product Launch 2025",
      "startDate": "2025-09-01",
      "endDate": "2025-09-07",
      "assets": {
        "logoUrl": "https://example.com/logos/launch-logo.png",
        "backgroundUrl": "https://example.com/backgrounds/launch-bg.png"
      }
    },
    {
      "id": "default",
      "name": "Default Theme",
      "assets": {
        "logoUrl": "https://blogger.googleusercontent.com/path/to/your/default-logo.png",
        "backgroundUrl": ""
      }
    }
  ]
}
```

### Step 2: Host the JSON File

Upload your `theme.json` file to a public web host. Free and easy options include:
-   [GitHub Gist](https://gist.github.com/)
-   [Netlify Drop](https://app.netlify.com/drop)
-   A public GitHub repository

After uploading, get the **raw URL** of the file. It should be a direct link to the `.json` file content.

### Step 3: Add the Script to Your Blogger Theme

1.  Go to your Blogger Dashboard.
2.  Navigate to **Theme**.
3.  Click the dropdown arrow next to "Customize" and select **Edit HTML**.
4.  Scroll to the very bottom of the editor and find the closing `</body>` tag.
5.  **Paste the entire script below** right before the `</body>` tag.

```html
<script>
//<![CDATA[
/* --- Automatic Event Theming Script (v2 - Recurring Events) --- */
document.addEventListener('DOMContentLoaded', function() {
    
    // --- CONFIGURATION ---
    // PASTE THE RAW URL TO YOUR theme.json FILE HERE
    const THEME_CONFIG_URL = 'YOUR_JSON_FILE_URL_HERE'; 

    // CSS Selectors for the Plus UI Theme
    const LOGO_SELECTOR = '.Header .headI img'; 
    const BACKGROUND_TARGET_ELEMENT = document.body;
    const MAIN_WRAPPER_SELECTOR = '.mainW';

    // Function to apply the theme assets
    function applyTheme(assets) {
        if (!assets) return;

        const logoElement = document.querySelector(LOGO_SELECTOR);
        const mainWrapper = document.querySelector(MAIN_WRAPPER_SELECTOR);

        // --- Apply Background ---
        if (assets.backgroundUrl && assets.backgroundUrl.trim() !== '') {
            BACKGROUND_TARGET_ELEMENT.style.backgroundImage = `url('${assets.backgroundUrl}')`;
            BACKGROUND_TARGET_ELEMENT.style.backgroundSize = 'cover';
            BACKGROUND_TARGET_ELEMENT.style.backgroundRepeat = 'no-repeat';
            BACKGROUND_TARGET_ELEMENT.style.backgroundAttachment = 'fixed';
            BACKGROUND_TARGET_ELEMENT.style.backgroundPosition = 'center center';
            
            if (mainWrapper) {
                const contentMaxWidth = getComputedStyle(document.documentElement).getPropertyValue('--contentW').trim() || '1280px';
                mainWrapper.style.maxWidth = contentMaxWidth;
                mainWrapper.style.margin = '0 auto';
                mainWrapper.style.boxShadow = '0 5px 25px rgba(0, 0, 0, 0.1)';
            }
        } else {
            // --- Revert to Default ---
            BACKGROUND_TARGET_ELEMENT.style.backgroundImage = '';
            if (mainWrapper) {
                mainWrapper.style.maxWidth = '';
                mainWrapper.style.margin = '';
                mainWrapper.style.boxShadow = '';
            }
        }

        // --- Apply Logo ---
        if (logoElement) {
            if (!logoElement.dataset.originalSrc) {
                logoElement.dataset.originalSrc = logoElement.src;
            }
            
            if (assets.logoUrl && assets.logoUrl.trim() !== '') {
                logoElement.src = assets.logoUrl;
            } else if (logoElement.dataset.originalSrc) {
                logoElement.src = logoElement.dataset.originalSrc;
            }
        }
    }

    // Main function to fetch config and apply the correct theme
    async function loadAndApplyTheme() {
        if (!THEME_CONFIG_URL || THEME_CONFIG_URL === 'YOUR_JSON_FILE_URL_HERE') {
            console.warn('Theme Changer: Please configure the URL to your theme.json file.');
            return;
        }
        
        try {
            const response = await fetch(THEME_CONFIG_URL, { cache: 'no-store' });
            if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
            
            const config = await response.json();
            const now = new Date();
            const currentYear = now.getFullYear();
            now.setHours(0, 0, 0, 0); // Normalize for date-only comparison

            const activeEvent = config.events.find(event => {
                if (event.id === 'default' || !event.startDate || !event.endDate) return false;

                let startDate, endDate;

                if (event.recurring) {
                    // Handle recurring annual events (format: MM-DD)
                    const [startMonth, startDay] = event.startDate.split('-').map(Number);
                    const [endMonth, endDay] = event.endDate.split('-').map(Number);

                    if (startMonth <= endMonth) {
                        // Event is within the same calendar year
                        startDate = new Date(currentYear, startMonth - 1, startDay);
                        endDate = new Date(currentYear, endMonth - 1, endDay);
                    } else {
                        // Event spans across the new year (e.g., Dec to Jan)
                        const currentMonth = now.getMonth() + 1;
                        if (currentMonth >= startMonth) {
                            // Currently in the latter part of the year
                            startDate = new Date(currentYear, startMonth - 1, startDay);
                            endDate = new Date(currentYear + 1, endMonth - 1, endDay);
                        } else {
                            // Currently in the early part of the new year
                            startDate = new Date(currentYear - 1, startMonth - 1, startDay);
                            endDate = new Date(currentYear, endMonth - 1, endDay);
                        }
                    }
                } else {
                    // Handle one-time events (format: YYYY-MM-DD)
                    startDate = new Date(event.startDate);
                    endDate = new Date(event.endDate);
                }
                
                // Ensure accurate date range comparison
                startDate.setHours(0, 0, 0, 0);
                endDate.setHours(23, 59, 59, 999);

                return now >= startDate && now <= endDate;
            });
            
            const themeToApply = activeEvent ? activeEvent.assets : config.events.find(event => event.id === 'default')?.assets;
            
            if (themeToApply) {
                console.log('Applying theme:', activeEvent ? activeEvent.name : 'Default');
                applyTheme(themeToApply);
            }

        } catch (error) {
            console.error('Theme Changer Error:', error);
        }
    }

    // Run the script
    loadAndApplyTheme();
});
//]]>
</script>
```

6.  **Crucially**, replace `YOUR_JSON_FILE_URL_HERE` in the script with the actual raw URL of your `theme.json` file.
7.  Click the **Save** icon.

## `theme.json` Configuration

The `events` array in your `theme.json` file contains objects, each representing a theme. Here are the properties for each event object:

-   `id` (string, required): A unique identifier for the event (e.g., `"tet"`, `"default"`). **You must have one event with `id: "default"`** to serve as a fallback.
-   `name` (string, required): A descriptive name for the event (e.g., `"New Year Celebration"`).
-   `recurring` (boolean, optional): Set to `true` for events that repeat annually. If omitted, the event is treated as a one-time occurrence.
-   `startDate` (string, required): The start date of the event.
    -   For **recurring** events: use `"MM-DD"` format (e.g., `"12-25"`).
    -   For **one-time** events: use `"YYYY-MM-DD"` format (e.g., `"2025-08-15"`).
-   `endDate` (string, required): The end date of the event. Uses the same format as `startDate`.
-   `assets` (object, required): Contains the URLs for the theme's visual elements.
    -   `logoUrl` (string): The full URL to the event-specific logo image.
    -   `backgroundUrl` (string): The full URL to the background image. Leave empty (`""`) for the default theme or if no background is desired.

## Troubleshooting

-   **Nothing happens:**
    1.  Press `F12` to open your browser's developer tools and check the **Console** tab for any red error messages.
    2.  Ensure you have replaced `YOUR_JSON_FILE_URL_HERE` with the correct raw URL.
-   **The theme doesn't update after I change the `theme.json` file:**
    -   The script is designed to bypass the cache (`cache: 'no-store'`), but your browser might still be caching the page. Do a hard refresh (`Ctrl+Shift+R` or `Cmd+Shift+R`).
-   **The logo changes but the background doesn't (or vice versa):**
    1.  Verify that the URL in your `theme.json` for the non-working asset is correct by pasting it into a new browser tab.
    2.  The CSS selectors (`LOGO_SELECTOR`, `MAIN_WRAPPER_SELECTOR`) might be incorrect if your theme has been heavily modified.
-   **The wrong event is showing:**
    -   Double-check the `startDate` and `endDate` formats in your `theme.json` file.
    -   Ensure your computer's date and time are set correctly.

---
Happy Theming!
