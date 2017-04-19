# ghost-serverpilot-autoupdate
Run these commands to quickly and easily update ghost managed via serverpilot

While I'm in love with Ghost, one thing that could definitely be improved is the upgrade process. Though you would think that because of how nice Ghost's interface is, they would have auto-update capabilities out-of-the-box (which I'm sure will come eventually), but currently it's a very manual process.

I've compiled a list of commands that should automate the process slightly. Though I could turn this into a bash script, it's just as easy to copy and paste these commands into a terminal window.

**Before running these, make sure you snapshot your DigitalOcean droplet. I'm not responsible if this breaks anything.**

Make sure you're running as root.
 
    sudo su

Then, `cd` to your app's root directory.

    cd /srv/users/serverpilot/apps/<APPNAME>

The following commands may need to be modified slightly, depending on how you're getting your ghost instance to run automatically. I personally use `pm2`, but I know of others who are using `forever`. Also, my pm2 instance is named `ghost`. If yours differs, you will need to alter that as well.

Keep in mind, this may take 3-4 minutes to run through completely. If you have a lot of traffic, you may need to do this during a maintenance window.

	pm2 stop ghost
	cp -r public public_backup
	wget https://ghost.org/zip/ghost-latest.zip
	unzip -o ghost-latest.zip -d public
	cd public 
	rm -rf content
	cd ..
	cp public_backup/config.js public
	cp public_backup/.htaccess public
	cp -r public_backup/content public
	cd public
	npm install --production
	pm2 start ghost
	cd ..
	chown -R serverpilot:serverpilot public
	rm ghost-latest.zip

And then, when you're absolutely **POSITIVE** that everything is back up and running like it's supposed to be:

	rm -rf public_backup
