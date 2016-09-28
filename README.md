# Kloud

Super simple screenshot sharing using your own S3 bucket, built with Node.js

### It goes like this
- You start Kloud.
- It watches a directory of your choice, for new files matching your defined criteria. (In my case it watches my Desktop for new screen shot pngs.)
- When a new file is detected, Kurtz Kloud saves the file to a bucket of your choice in S3, copies the file's S3 URL to your clipboard, and displays a notification.

![Terminal output](https://s3.amazonaws.com/kurtzkloud.com/p/kurtz-kloud-terminal.png)
![Native notification](https://s3.amazonaws.com/kurtzkloud.com/p/kurtz-kloud-notification.jpg)

## Requirements
- Node.js ([http://nodejs.org/]())

## Install
```
git clone git@github.com:ltk/kurtz-kloud.git
cd kurtz-kloud
npm install
```

## Configure
```
cp config.json.example config.json
```

Edit `config.json` to suit your needs. Your S3 bucket must already exist. Kloud will not create a bucket if the configured bucket is not found.

If you want your images to be publicly accessible (if you want to share them with other people) you may need to add a bucket policy to your S3 bucket similar to the one below that makes the getObject action across the entire bucket accessible to everyone:

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "AddPerm",
			"Effect": "Allow",
			"Principal": {
				"AWS": "*"
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::YOUR_BUCKET_NAME_HERE/*"
		}
	]
}
```

## Run
```
cd kurtz-kloud
npm start
```

### Start at Boot
#### In OS X / MacOS
Make a file in your `~/Library/LaunchAgents` folder called something like `com.github.ltk.kurtz-kloud.plist` with the following contents:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
	<dict>
		<key>Label</key>
		<string>com.github.ltk.kurtz-cloud</string>

		<key>ProgramArguments</key>
		<array>
		        <!-- Or wherever your npm binary is located -->
			<string>/usr/local/bin/npm</string>
			<string>start</string>
		</array>

		<key>WorkingDirectory</key>
		<!-- Replace this with wherever you cloned the repo to -->
		<string>/Users/[ME]/Applications/kurtz-kloud</string>

		<key>KeepAlive</key>
		<true />

		<key>RunAtLoad</key>
		<true />

		<key>EnvironmentVariables</key>
		<dict>
			<key>PATH</key>
			<string>/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
		</dict>

		<key>StandardOutPath</key>
		<string>/dev/null</string>

		<key>StandardErrorPath</key>
		<!-- Or wherever you want errors logged to -->
		<string>/Users/[ME]/Library/Logs/kurtz-kloud.err.log</string>
	</dict>
</plist>
```

Save this and run `launchctl load ~/Library/LaunchAgents/com.github.ltk.kurtz-kloud.plist`.
