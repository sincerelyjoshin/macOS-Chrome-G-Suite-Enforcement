## OBECTIVE
Require macOS clients to log into Chrome to enforce policies set at Google Admin Console.

All http:// and https:// traffic will be blocked besides the Google accounts login page (https://accounts.google.com). Once a user is logged in, http and https traffic will be restored and users will be subject to all additional policies configured in the Google Admin Console.

This is accomplished via 3 components. Basic knowledge of Google Chrome policy is recommended to understand how the 3 components work with each other. https://support.google.com/chrome/a/answer/187202

**COMPONENTS:**

  1. Launch Agent - blacklists all http:// and https:// traffic (Policy Level - Recommended)

  2. Configuration Profile - whitelists accounts.google.com and restricts which domains can log into Chrome (Policy Level - Mandatory)

  3. Chrome Management from Google Admin Panel - blacklists foo://bar and adds all http:// and https:// traffic to the blacklist exeption(Policy Level - Mandatory)

## LAUNCH AGENT

The launch agent will run the following command on user login:
```sh
defaults write com.google.Chrome URLBlacklist -array '"http://*"' '"https://*"'
```
This will block all http and https traffic when using Google Chrome at the Recommended policy level. A policy that is at Recommended level can be easily overwritten

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Label</key>
	<string>com.yourcompany.studentgoogleconfig</string>
	<key>LowPriorityIO</key>
	<false/>
	<key>Nice</key>
	<integer>0</integer>
	<key>ProgramArguments</key>
	<array>
		<string>/bin/sh</string>
		<string>-c</string>
		<string>defaults write com.google.Chrome URLBlacklist -array &apos;&quot;http://*&quot;&apos; &apos;&quot;https://*&quot;&apos;</string>
	</array>
	<key>RunAtLoad</key>
	<true/>
</dict>
</plist>
```

## CONFIGURATION PROFILE

The Configuration Profile for preference domain com.google.Chrome will whitelist the URL accounts.google.com so that users can access the Google login page. The ``RestrictSigninToPattern`` key will also be managed here to limit which domains can login. Example Plist:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>RestrictSigninToPattern</key>
	<string>*@yourcompany.com</string>
	<key>URLWhitelist</key>
	<array>
		<string>accounts.google.com</string>
	</array>
</dict>
</plist>
```

## CHROME MANAGEMENT - GOOGLE ADMIN CONSOLE

From <a href="https://admin.google.com/">Google Admin Console</a>, Chrome management needs to be configured with a URL Blacklist and a URL Blacklist Exception. I recommend setting the blacklist to a bogus URL with a bogus protocol: ``foo://bar``. The Blacklist Exception needs to wildcard all ``http://*`` and ``https://*`` traffic.

![Chrome Management](https://github.com/sincerelyjoshin/macOS-Chrome-G-Suite-Enforcement/blob/master/GoogleAdminConsole-ChromeManagement.png)
