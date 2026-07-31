# Troubleshooting

Quick fixes for the snags people hit most often. Because everyone scans a different app here, most snags come down to two moves: give the agent a little more context about your app, and if that does not clear it, reach out to support@stackhawk.com.

## `hawk: command not found`

* Open a new terminal window after installing the CLI, then run `hawk version` again. A fresh terminal picks up the newly installed command.

## The scan fails to connect, or errors partway through

* The app has to stay running the whole time you scan. If you stopped it, start it again and rescan.
* Confirm the app answers at its URL in a browser before you scan, and that the URL you gave the agent matches where the app is actually serving.

## The agent doesn't seem to have the StackHawk skills

* Ask it: **"What StackHawk skills do you have?"** It should list HawkScan scanning, API reporting, and more. If it doesn't, revisit **Part 2** for your agent and confirm the install.

## The scan didn't reach the pages behind the login

* The agent usually works out the login from the source, but it cannot always tell how your app authenticates. If it only scanned public pages, tell it how to sign in, for example: the app has a form login at `/login`, here are test credentials, and scan the pages behind it too. A token or OAuth flow works the same way, just describe it.

## The agent is hesitant to change the app

* If your agent hesitates to edit the code, tell it to go ahead and fix the findings the scan reported. It is your app to change.

## `hawk init --browser` can't open a browser

* Use the manual method instead: create an API key in the StackHawk platform (**Settings > API Keys**), then run `hawk init` and paste the key when prompted. See the "prefer to set the key manually" note on your OS setup page.

---

If you run into something not covered here, reach out to our support team at support@stackhawk.com. We're happy to help.
