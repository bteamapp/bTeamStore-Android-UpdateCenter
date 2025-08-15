
# bTeamStore-Android-UpdateCenter

Update center and APK repository for bTeamStore Android applications.


## Description

This repository contains the source code for the update center of bTeamStore Android applications, as well as the APK installation file repository for these applications.

**Key Features:**

*   **Automatic Updates (bTeam Self-installer):** This update center supports Android applications with self-updating capabilities through the **bTeam Self-installer** API. This API supports two modes of operation:
    *   **Automatic:** The application automatically checks for a new version on startup. If an update is available, the update center displays a notification to the user.
    *   **Manual:** The application only checks for a new version when the user manually clicks the "Check for Updates" button (or similar) in the application.
    *   **How it works:** The application sends a request to the update center server along with the current version information. The server checks and responds with a link to download the latest APK file if available. If there is no newer version, the server will return "none". This data exchange uses the .txt file format via the HTTP protocol.
*   **Download and Install APKs:** Users can directly download and install the latest APK files of bTeamStore applications from this update center.
*   **APK Repository:** This update center acts as an App Center, providing a repository of APK installation files for bTeamStore applications. Users can easily access and download different versions of applications.
*   **Update Notifications:** When a new version is available (in Automatic mode), the update center will display a notification to the user, allowing them to choose to update immediately or postpone.
*   **Version Management:** The update center allows managing different application versions, making it easy for users to revert to an older version if needed.

## Directory Structure

```
Root
├── frontend             // bTeam's frontend area (you don't need to worry about this)
│   └── [package_name]
├── source               // API source code
│   ├── android          // Source code for the Android application (update center)
│   └── web              // Server-side source code (API)
└── docs                 // Documentation
```

*   **`frontend`**: This directory contains the frontend source code for bTeam applications. You do not need to concern yourself with this directory.
*   **`source`**: This directory contains the main source code of the project.
    *   **`android`**: Contains the source code for the Android application, including the user interface of the update center and update handling logic (bTeam Self-installer).
    *   **`web`**: Contains the server-side source code, providing the **bTeam Self-installer** API to check and return update information.
*   **`docs`**: This directory contains documentation, including installation instructions, **bTeam Self-installer** API integration guide, and other related documents.

## Installation

To deploy and use the update center, you need to:

1. **Clone the repository:** Clone this repository to your local machine.
2. **Open the project:** Open the project in Android Studio (for the `source/android` source code).
3. **Configuration:** Modify the necessary configuration parameters (e.g., APK storage path, server information) in the corresponding configuration files.
4. **Build and run:** Build and run the project on an Android device or emulator (for `source/android`). For the `source/web` source code, you need to deploy it to a suitable server.

**For applications that want to integrate self-updating capabilities (bTeam Self-installer):**

1. **Add library:** Add the necessary library or module from this repository (`source/android`) to your application project.
2. **Integrate bTeam Self-installer:** Use the provided functions and methods to implement the **bTeam Self-installer** mechanism. Specifically, the application will:
    *   Send an HTTP request to the update center server (deployed from `source/web`), along with the current version information and update mode (Automatic or Manual) in a .txt file.
    *   Receive a response from the server:
        *   If a new update is available: The server returns a link to download the latest APK file.
        *   If no update is available: The server returns "none".
    *   If an update is available and the mode is Automatic, the application will display a notification to the user. If in Manual mode, the application will only display a notification when the user requests to check.
    *   Proceed to download and notify the user to install if the user agrees.
3. **Configure Manifest:** Ensure that the application's Manifest file is correctly configured to allow application installation from sources other than the Google Play Store (if necessary).

## How to Use

**For End Users:**

*   **Automatic Updates:** When a bTeamStore application has a new version and has integrated **bTeam Self-installer** in **Automatic** mode, the update center will automatically check and display a notification to you. You can choose "Update" to download and install the latest version.
*   **Manual Updates:** If the application is integrated with **bTeam Self-installer** in **Manual** mode, you need to go to the application's settings and click the "Check for Updates" button (or similar). If a new version is available, you will receive a notification to proceed with the update.
*   **Manual APK Download:** You can access the "App Center" section (or similar) in the application to see a list of available bTeamStore applications. Here, you can select the desired application and version to download the APK file and install it manually.

**For Developers:**

*   **Integrate bTeam Self-installer:** Refer to the detailed instructions in the `docs` directory to integrate the **bTeam Self-installer** self-updating feature (including both Automatic and Manual modes) into your application.
*   **Manage APKs:** Use the provided tools or management interface to upload new APK versions to the update center.

## Contributing

We welcome all contributions to the bTeamStore-Android-UpdateCenter project! If you would like to contribute, please follow these steps:

1. **Fork the repository:** Create a fork of this repository on your GitHub account.
2. **Create a new branch:** Create a new branch from the `main` branch to make your changes, we only allowed to make changes in 'source' folders.
3. **Make changes:** Write code, fix bugs, or add new features on your branch.
4. **Test:** Ensure that your changes work correctly and do not cause errors.
5. **Create a Pull Request:** Submit a Pull Request from your branch to the `main` branch of the original repository.
6. **Discussion and Review:** We will review, discuss, and merge your changes if appropriate.

Please refer to the `CONTRIBUTING.md` file (if available) for more details on the contribution process.

## License

This update center is licensed under the MIT License. Please see the [LICENSE](LICENSE) file for more details.

## Contact

If you have any questions, suggestions, or bug reports, please contact us via email: **bteamapp@gmail.com**

We are always ready to support and improve the project.

## Future Plans

*   Support for update channels (e.g., Beta, Stable).
*   Integration of a push notification system for faster update notifications.
*   Provide an API to allow third-party applications to integrate with the update center (besides bTeam Self-installer).
*   Develop a web interface to manage applications and versions more easily.

Thank you for your interest in the bTeamStore-Android-UpdateCenter project!
```
