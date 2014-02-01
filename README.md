rsyncer
=======

*I sync, therefore I am.*


what's this?
------------

`rsyncer` lets you make configurations for `ssh` and `rsync`. It's a little like ssh configs, except:

- **JSON**-based config file!
- **Variables:** define your own variables. Variables can be set in any order.
- **Inheritance:** configs can extend other configs.
- **Other programs:** send arguments to any program, not just `rsync` and `ssh` and other programs that support ssh configs.


quick start
-----------

Download `rsyncer` and move it into one of the directories in `PATH`.

	mkdir ~/.config # if it doesn't already exist
	mkdir ~/.config/rsyncer
	cd ~/.config/rsyncer
	touch main.json

Add a config "mysite", either in `main.json`:

	{
		"mysite": {
			"host": "johndoe@example.org",
			"remote": "$host/www",
			"local": "~/Sites/mysite"
		}
	}

or in `mysite.json`:

	{
		"host": "johndoe@example.org",
		"remote": "$host/www",
		"local": "~/Sites/mysite"
	}

Good to go!

	$ rsyncer mysite pwd
	/Users/User/Sites/mysite
	$ rsyncer mysite ssh
	ssh johndoe@example.org
	...
	$ rsyncer mysite push
	rsync -rvut /Users/Sean/Sites/mysite/ johndoe@example.org/www
	...
	$ rsyncer mysite pull
	rsync -rvut johndoe@example.org/www/ /Users/Sean/Sites/mysite
	...

setup
-----

Here's an example config at `~/.config/rsyncer/main.json`:

	{
		"server:example.com": {
			"host": "username@$server",
			"server": "example.com",
			"rsync-path": "/home/username/opt/rsync/bin/rsync",
			"rsync-args": ["--copy-unsafe-links"]
		},
		"myweb": {
			"extends": "server:example.com",
			"local": "~/Sites/MyWeb",
			"remote": "$host:/home/username/public_html",
			"#exclude-from": "$local/.exclude"
		},
		"project1": {
			"extends": "server:example.com",
			"local": "~/Sites/Personal",
			"remote": "$host:~/subdomains/project1",
		},

		"server:cs.berkeley.edu": {
			"exclude-from": "$local/.exclude",
			"servername": "star",
			"server": "$servername.cs.berkeley.edu",
			"host": "$username@$server",
			"remote": "$host:work",
			"local": "~/Documents/School/cs/$username",
			"rsync-args": ["--copy-unsafe-links"]
		},
		"cs61a": {
			"extends": "server:cs.berkeley.edu",
			"username": "cs61a-ab",
		},
		"cs61c": {
			"extends": "server:cs.berkeley.edu",
			"username": "cs61c-cd",
			"servername": "hive15",
			"remote": "$host:work/my",
			"local": "~/Documents/School/cs/$username/my"
		},
		"ee20n": {
			"extends": "server:cs.berkeley.edu",
			"username": "ee20n-ef",
			"local": "~/Documents/School/ee/$username",
		}
	}

You can also store each config in its own file. Example (`~/.config/rsyncer/testsite.json`)

	{
		"host": "username@$server",
		"server": "test.example.com",
		remote: "$host:www",
		local: "~/Sites/Test",
		"disable-pull": true
	},
