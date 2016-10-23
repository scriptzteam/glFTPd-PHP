# glFTPd-PHP
We do like things in PHP, why not a WEB panel for glFTPd? So here we come ;)

"The most basic thing you can do is manage users via web panel aka glFTPd-PHP. Then there are a lot you can do!"

The beta release was released to those who deserve it in da scene, other way to obtain it? mmm, let you guess.

# Home index
![alt tag](http://i.imgur.com/1ntXPYF.png)
# Support obfuscated view too, so no site usernames as output
![alt tag](http://i.imgur.com/LNwoeDa.png)

# glFTPd-PHP supported SITE commands

 USERMANAGEMENT COMMANDS:
 ------------------------
site adduser <string> <string> [string@<IPmask|hostmask> ...]
	Adds a user with first argument as username and second as password.

	See "site renuser" for info and restrictions for the username.

	See "site chpass" for info and restrictions for the password.

	The optional arguments can be max 5 and are ident@IP's for the user.

	If a user is added by a groupadmin, that user will have the GLOCK flag
	enabled and will inherit default values from the "default."<groupname>
	file if existing, or "default.user" otherwise. The latter will also be
	used for users not being added by a groupadmin.
	
	Example: site adduser Archimede mypassword
	This would add the user "Archimede" with the password "mypassword".

	Example: site adduser Archimede mypassword *@127.0.0.1
	This would do the same as above and add the ip '*@127.0.0.1' at the
	same time.

site addip <user> <string@IPmask|hostmask> [string@IPmask|hostmask ...]
	Add an ident@IP to the user. Max 10 at a time.

	Example: site addip Archimede Archimede@127.0.0.1 *@10.10. no@*.isp.net
	This would add the ident@IP "Archimede@127.0.0.1", ipmask "*@10.10.*"
	and hostmask "no@*.isp.net" to user "Archimede". The * at the end is
	optional and auto-added.

	Example: site addip lamer someident@3.1.2.1[2-5]?
	This will add the mask matching IP's 3.1.2.120 through 3.1.2.159.
	Warning: you can't use [] to declare a range of numbers > 9, so [20-59]
	will not work. Use [2-5]? or [2-5][0-9] instead. Ranges that don't start
	at a power of 10 or don't end at 9 are not possible unless you add
	more than 1 mask.

site change <user> <string> <string>
site change <=group> <string> <string>
site change <{> <user> [user ...] <}> <string> <string>
site change <*> <string> <string>
	Changes a setting for a user or users.
	The second argument in any of these commands is a user's setting.
	The argument following it will be the new value, if accepted.

	The first command changes the value for one user, the second for all
	users in the group, the third for all users in the list (max 30) and the
	fourth command is for all users.

	Available settings:

	Setting		Value	Description
	------------------------------------------------------------------------
	ratio		<#>	Upload/Download ratio, max 9, min 0 = Unlimited.
	wkly_allotment	[#,]<#>	Number of KiB this user will be given once a
				week; needs the reset binary enabled in crontab.
				First number is the section number (default 0),
				second the number of KiB to give.
				User's credits are replaced, not added to.
				Only one section at a time is supported.
				Changing this immediately replaces the amount if
				the user had less credits.
	homedir		[path]	Changes the user's homedir.
				This command is disabled by default, see the
				"min_homedir" setting in glftpd.conf to enable.
				Important: don't use a trailing '/' in the path!
				Users CANNOT cd, list, upload/download, etc,
				outside of their homedir. It acts similarly to
				chroot() (try man chroot).
				When no value is given, the current homedir is
				shown.
	startup_dir	<path>	The directory to start in when logging in.
				This is /rootpath/homedir/path.
				Example: if path is "/incoming", rootpath is
				"/glftpd" and homedir is "/site", then the user
				will start in "/glftpd/site/incoming".
				Users CAN cd, list, upload/download, etc,
				outside of startup_dir.
	idle_time	<#>	Sets the default and maximum idle time for this
				user (overrides the -t and -T settings on glftpd
				command line). If -1, it is disabled; if 0, it
				is the same as the idler flag.
	expires		<string> The date a user expires, after this date a user
				will no longer be able to login. Format used is
				"yyyy-mm-dd", use "never" or "0" to disable.
	credits		<#>	Replace credits in KiB left to download.
	flags		<-|+><flag>[[-|+flag][flag ...] ...]
				Type 'site flags' for a list of flags.
	num_logins	<#> [#]	Number of simultaneous logins allowed, second
				number is number of simultaneous logins from the
				same IP. Max is 30000.
	timeframe	<#> [#]	The hour from which to allow logins and the hour
				when logins from this user will be rejected.
				This is set in 24 hour format, min 0 and max 23.
				If a user is online past its timeframe, the user
				will be disconnected at the next 'CWD'.
	time_limit	<#>	Time limits, per LOGIN SESSION, in minutes.
				0 = Unlimited.
	tagline		<stringlist> User's tagline (max 63 symbols).
	comment		<stringlist> Changes the user's comment (max 49 symbols).
	max_dlspeed	<#>	Downstream bandwidth control in KiB/s.
				0 = Unlimited.
	max_ulspeed	<#>	Same but for upstream.
	max_sim_down	<#>	Maximum number of simultaneous downloads for the
				user (-1 = Unlimited, 0 = None, max 30000).
	max_sim_up	<#>	Same but for uploads.
	sratio		<string> <#> Changes the ratio of a section, given as
				first argument by its name (other than DEFAULT).
				Use -1 to use the DEFAULT ratio, max is 9.

	Available flags:

	Flagname       	Flag	Description
	------------------------------------------------------------------------
	SITEOP		1	Siteoperator, has access to most commands.
	GADMIN		2	Groupadmin of at least one of the user's public
				groups,	private groups have no such thing.
				Use 'site chgadmin' to give or take this flag.
	GLOCK		3	User cannot change group.
	EXEMPT		4	By default allows to log in when site is full,
				to do 'site idle 0' (same as IDLER flag) and
				exempts from the sim_xfers limit in config file.
	COLOR		5	Enables having colors in listings and other
				serverreplies. Can also be changed via the
				'site color' command.
	DELETED		6	User is deleted.
				Use 'site deluser' to give or 'site readd' to
				take this flag.
	USEREDIT	7	"Co-Siteop"
	ANONYMOUS	8	Anonymous user (per-session like login).
				No DELE, RMD or RNTO/RNFR; '!' on login is
				ignored and the userfile doesn't get updated
				with stats (serves as template - use external
				scripts if transfer stats must be recorded).
	NUKE		A	Allows to use 'site nuke/reqlog'.
	UNNUKE		B	Allows to use 'site unnuke/reqlog'.
	UNDUPE		C	Allows to use 'site undupe/predupe'.
	KICK		D	Allows to use 'site kick'.
	KILL		E	Allows to use 'site kill/swho'.
	TAKE		F	Allows to use 'site take'.
	GIVE		G	Allows to use 'site give'.
	USERS		H	Allows to use 'site user/users/ginfo'.
	IDLER		I	Allows to idle forever.
	CUSTOM1		J	Custom flag 1
	CUSTOM2		K	Custom flag 2
	CUSTOM3		L	Custom flag 3
	CUSTOM4		M	Custom flag 4
	CUSTOM5		N	Custom flag 5

	Custom flags can be used in the config file to give some users access to
	certain things without having to use private groups. These flags will
	only show up in 'site flags' if they're turned on. Custom flags up to
	'Z' can be used.

	Note: flag 1 is not GOD mode, you must have the correct flags for the
	actions you wish to perform.
	Note: a user with flag 1 DOES NOT WANT flag 2.
	Note: flags A-H can have their access changed through the -flag
	permissions in the config file.

	Example: site change Archimede ratio 5
	This would set the ratio to 1:5 for the user "Archimede".

site chgadmin <user> <group>
	Toggle the users gadmin status for the given group.

	When a user becomes a gadmin he will automatically get flag 2 added.
	When a user stops to become a gadmin of any of his groups the gadmin
	flag will be automatically removed.

site chgrp <user> [group ...]
	Adds and/or removes a user from (a) group(s), max 20 at a time (10 to
	remove from and 10 to add to; put groups to remove first). When adding a
	user to a group, the group must have available slots left, even when
	being a siteop.

	When no group is provided, lists the groups the user is member of.

	Example: site chgrp archimede ftp
	This would add "archimede" to the "ftp" group if not already a member,
	otherwise "archimede" is removed from "ftp".

site chpass <user> <string> 
	Change user's password to the second argument.

	See the "secure_pass" setting if a password can't be changed with error
	"Password is not secure enough".

	The password should be max 20 symbols or can be the special symbols '*'
	or '@'. The first means any password will let the user log in, the
	second that the password has to be an email address.

site chpgrp <user> <group>
	Sets the primary group for a user. If the user is not yet a member, the
	user is added to the group and the group is set as primary group. When
	adding a user to a group, the group must have available slots left, even
	when being a siteop.

	Example: site chpgrp archimede ftp
	This would change the primary group to 'ftp' for the user 'archimede',
	adding the user to it if not yet a member and with slots available.

site delip <user> <#|string@<IPmask|hostmask>> [#|string@<IPmask|hostmask> ...]
	Delete an IP from user by giving the exact "string@IPmask" or
	"string@hostmask" as added to the user, or use the ranknumber (see
	'site user "user"'). Max. 10 arguments, excluding the user-argument.

	Example: site delip Archimede Archimede@127.0.0.1
	This removes the ident@ip 'Archimede@127.0.0.1' from user "Archimede".

	Example: site delip Archimede 1
	This removes ident@ip #1 from user "Archimede".

site deluser <user>
	Delete a user, may be readded with 'site readd'.
	In order to delete a user permanently, 'site purge' must be used.

	Example: site deluser Archimede
	This will activate the DELETED (6) flag for user "Archimede".

site gadduser <group> <string> <string> [string@<IPmask|hostmask> ...]
	Adds a user and adds it to the given group at the same time. If a
	"datapath/users/default.group" file exists, it will be used as a base
	instead of "default.user".

        Only public groups can be used as group. When adding a user to a group,
	the group must have available slots left, even when being a siteop.

	See "site adduser" for more info on syntax.

site purge [user]
	Removes deleted users permanently, readding with 'site readd' will NOT
	work for purged users.

	If no argument is given, then all the deleted users will be removed.

site readd [user]
	Shows a list of deleted users who can be readded (same as
	'site users deleted') or readds the given user (have flag 6 removed).

	Note: when readding a user, all groups the user was member of need to
	have available slots, even when being a siteop.

	ex. site readd Archimede
	This will remove the DELETED flag for user "Archimede".
	
site renuser <user> <string>
	Rename a user to the second argument.

	Username can be max 23 symbols and may not contain '/' or ':', not start
	with "default." or end with ".lock" or ".[1-9][0-9]*".

	Example: site renuser usurper Usurper
	This will make usurper look bigger and meaner.

site seen <user>
	Check when a user was last online.

	Example: site seen Archimede
	This will display the last time "Archimede" logged in.

site show <user>
	Displays the given user's userfile in raw format as it exists on disk.

site user [user]
	Lists all non-deleted users or shows detailed info about the given user,
	definable in "rootpath/datapath/text/user.{comment,txt,extra}".

site users [string[*]|flag|=group]
	Lists users and shows their primary group, UL/DL stats and credits.
	Without an argument, all users except deleted are listed.
	Deleted users are never shown except when using "deleted" as argument.

	When the argument is a group, only members of that group are listed.
	When it is a flag, only users with that flag are listed.

	When the argument ends with a '*' (and does not start with a "="), the
	symbols before it are interpreted as the first part of a username. All
	users with a username starting with those symbols will then be listed.

	If the argument is "help", usage info is shown.

	Other possible arguments, apart from flag, group and partial username:
	"*", "all", "anonymous", "deleted", "exempt", "gadmin", "give", "glock",
	"idler", "kick", "kill", "leech", "nuke", "siteop", "take", "undupe",
	"unnuke", "useredit" and "users".

	Example: site users f*
	This will list all usernames starting with "f": frank, frog, ...


 GROUPMANAGEMENT COMMANDS:
 -------------------------
site ginfo [group]
	Shows detailed info about a group.
	If no argument is given and the logged in user is gadmin, then the first
	group where the logged in user is gadmin of is used.

	Deleted users have their tagline replaced with "   ***DELETED***".

site groups
	Shows available groups.

site grp <group>
	Display group settings, only for public groups.

site grpadd <string> [stringlist]
	Add a new group, max 14 symbols excluding non-printable, space and ':'.
	Second argument is the group description, max 39 symbols also excluding
	any non-printable and ':'. By default the groupname is used.

	Example: site grpadd group new_group
	This would add the group "group" with the description "new_group".

site grpchange <group> <string> <string>
site grpchange <{> <group> [group ...] <}> <string> <string>
site grpchange <*> <string> <string>
	Changes a setting for a group or groups.
	The second argument in any of these commands is a group's setting.
	The argument following it will be the new value, if accepted.

	The first command changes the value for one group, the second for all
	groups in the list (max 30) and the third command is for all groups.

	Available settings:

	Setting		Value	Description
	------------------------------------------------------------------------
	slots		<#>	The available number of slots, this includes
				leech and allot slots. -1 is Unlimited.
	leech_slots	<#>	The available number of leech slots.
				-1 is Unlimited, -2 is Disabled.
	allot_slots	<#>	The available number of weekly allotment slots.
				-1 is Unlimited, -2 is Disabled.
	max_allot_size	<#>	The max weekly allotment size that a gadmin can
				give to a member of the group. 0 is Unlimited.
	max_logins	<#>	The max number of members of the group that can
				login simultaneously.
	comment		<stringlist> The comment for this group, max 63 symbols.

site grpdel <group>
	Delete a group, only possible when there are no members anymore.

site grpnfo <group> <stringlist>
	Change description for a group, see "site grpadd" for restrictions.

site grpren <group> <string>
	Renames a group, see "site grpadd" for naming restrictions.

	Example: site grpren ftp new_ftp
	This would change the group name "ftp" to "new_ftp".
	All users in group "ftp" will now automatically belong to "new_ftp".


 STATISTIC COMMANDS:
 -------------------
site aldn [#] [#|string] [=group]
	Display alltime downloaders. All arguments can be left out or used in
	almost any combination or order.

	First argument controls the number of users shown.
	Second argument is a section name or number (0-9). If section is a
	number, it needs to be preceeded by the number of users shown.

	This is the same for all statistic commands (wkup, gpal, nuketop, etc).

	Specifying a group will only show users who belong to that group, but
	the user doing it has to have special access in glftpd.conf to do it.
	This is controlled by the -grpstats permission.

	This is the same for all user statistic commands (wkup, nuketop, etc).

	Example: site aldn =Friends
	Shows top 10 of alltime downloaders in group "Friends".

	Example: site aldn 20 LinuxSection =Leechers
	Shows top 20 of alltime downloaders in group "Leechers" for the
	"LinuxSection" section.

site alup [#] [#|string] [=group]
	Display alltime uploaders. See "site aldn" for info.

site daydn [#] [#|string] [=group]
	Display daytop downloaders. See "site aldn" for info.

site dayup [#] [#|string] [=group]
	Display daytop uploaders. See "site aldn" for info.

site gpad [#] [#|string]
	Display alltime group downloaders. See "site aldn" for info.

site gpal [#] [#|string]
	Display alltime group uploaders. See "site aldn" for info.

site gpdaydn [#] [#|string]
	Display daytop group downloaders. See "site aldn" for info.

site gpdayup [#] [#|string]
	Display daytop group uploaders. See "site aldn" for info.

site gpmonthdn [#] [#|string]
	Display monthtop group downloaders. See "site aldn" for info.

site gpmonthup [#] [#|string]
	Display monthtop group uploaders. See "site aldn" for info.

site gpwd [#] [#|string]
	Display weektop group downloaders. See "site aldn" for info.

site gpwk [#] [#|string]
	Display weektop group uploaders. See "site aldn" for info.

site monthdn [#] [#|string] [=group]
	Display monthtop downloaders. See "site aldn" for info.

site monthup [#] [#|string] [=group]
	Display monthtop uploaders. See "site aldn" for info.

site nuketop [#] [#|string] [=group]
	Display alltime nuketop. See "site aldn" for info.

site stats [user]
	Display the logged in or given user's upload and download statistics
	for all sections.

	Definable in "rootpath/datapath/text/user.stats"; for other sections
	than DEFAULT, filenames called "SECTIONuser.stats" must exist.

site traffic
	Display total, month, week and day uploads/downloads by all existing
	users in all sections.

site wkdn [#] [#|string] [=group]
	Display weektop downloaders. See "site aldn" for info.

site wkup [#] [#|string] [=group]
	Display weektop uploaders. See "site aldn" for info.


 DATABASE COMMANDS:
 ------------------
site dupe [-"max" <#>] [-"from" <string>] [-"to" <string>] <stringlist>
	Searches the dupe database ("rootpath/datapath/logs/dupelog") for
	directories that have been created on site matching ALL search strings.
	Searching a big database can take quite some time so please be patient
	and try not to use common words. Do NOT include wildcards!
	Searchstrings are case insensitive.
	
	If -max is not specified, glftpd defaults to 20 matches. Matches are
	taken from the bottom of the dupelog (newest entries are at the bottom).

	The format for the "-from" and "-to" options is "MMDDYY".

	Example: site dupe quake
	This will search the database for the string "quake".

	Example: site dupe quake arena
	This will find "Quake_Arena" but not just "Quake".

	Example: site dupe -from 010199 -to 010100 duke nukem 3D
	This will search for "duke", "nukem", and "3D" in all of 1999.

	Example: site dupe -max 50 linux glftpd 1.
	This will show up to 50 entries with "glftpd", "linux" and "1." in the
	name, like "Glftpd.for.linux.v1.20" or "glftpd.v1.19_for_linux".

site fdupe [#] <string>
	Searches the dupe database ("rootpath/datapath/logs/dupefile") for files
	that have been created on site matching the search string.
	Searching a big database can take quite some time so please be patient
	and try not to use common words. Do NOT include wildcards!
	Searchstring is case insensitive.

	The first argument limits the results, default 20 matches. Matches begin
	at the bottom of the dupefile (newest entries are at the bottom).
	A limit must be used if the searchstring is an integer as well.

	Example: site fdupe sample
	This will search the database for the string "sample".

	Example: site fdupe 10 vivaldi
	This will show only the 10 newest files with vivaldi in them.

site predupe [stringlist/]<file>
	Adds the given file to to the file dupe database located at
	"rootpath/datapath/logs/dupefile" (rest is ignored).

	Only files not matching any filemasks at "ignore_type" settings in
	config file are added.

	The file dupe database is automatically "compressed" after every 1000th
	added file in average by removing all entries older than your days limit.

site search <stringlist>
	Searches the directory database ("rootpath/datapath/logs/dirlog") for
	directories on site matching ALL search strings.
	Searchstrings are case insensitive.

	If this command finds old non-existing directories, you need to run the
	"olddirclean2" utility to clean your dirlog. Best run it from crontab.

	Example: site search quake
	This searches the database for directories containing "quake".

	Example: site search linux gnu
	This will find all directories that contain both "linux" and "gnu".

site undupe [stringlist/]<filemask>
	Removes all files matching the filemask from the file dupe database
	located at "rootpath/datapath/logs/dupefile" (rest is ignored).

	Removal is only done for files not older than the "dupe_check" setting's
	first argument and not matching any filemasks at "ignore_type" settings
	in config file.

	Example: site undupe cls*
	This removes all files starting with "cls" from the file dupe database.

site update <dirmask>
	Adds all directories matching the given argument in the current working
	directory to the dirlog database.

	Note: this does not work recursively and skips directories without
	permissions higher than 0700.

	Example: site update A*
	This adds all directories beginning with "A" in the current working
	directory to the dirlog database.


 LOG COMMANDS:
 -------------
site errlog [#] [string]
	Display the error log ("rootpath/datapath/logs/error.log").
	See "site syslog" info for syntax.

site laston [#] [-user] [=group] ["A"|"D"|"L"|"N"|"T"|"U"]
	Displays certain infos about the last users that were online (from
	"rootpath/datapath/logs/laston.log").
	Only works when the "lastonline" setting is enabled in config file.

	First argument is number of entries, second only shows entries from the
	given user, third only from users with the given group as primary group
	and the third argument filters entries for type.
	All arguments can be combined as chosen, only one of each at a time.
	By default 10 entries are shown without any filters.

	The types can be: A (Added user), D (Download), L (Lost connection), 
			  N (Nuked a dir), T (Timed out), U (Upload).

site logins [#] [string]
	Display the login log ("rootpath/datapath/logs/login.log").
	See "site syslog" info for syntax.

site new ["."] [#]
	Shows newest created directories (from "rootpath/datapath/logs/dirlog").

	If first argument is given (single dot), then only newest directories
	in the current directory tree are shown.
	The last argument limits the amount of directories shown, default 10.

	Note: users only see directories created under their home directory.
	For a homedir being /site/public, no directories created in
	/site/private are shown by using site new.
	To achieve this, put a %NEW cookie in a text file with "/site/private"
	as a parameter  and use msgpath or a custom command to display it.

	Example: site new . 20
	This will show the last 20 dirs made in the current dir tree.

site nukes [#]
	Show recently nuked releases (from "rootpath/datapath/logs/nukelog").

	Argument limits amount of shown releases, default 10.

site reqlog [#] [string]
	Display the request log ("rootpath/datapath/logs/request.log").
	See "site syslog" info for syntax.

site syslog [#] [string]
	Display the user changes log ("rootpath/datapath/logs/sysop.log").

	Without any arguments, the whole log is shown. Use the first argument to
	only show those lines that are at max that many days old.

	If anything else than a number is used, only those lines containing the
	string are shown; this also matches the date and timestamps.
	Use two arguments if the string is a number. If the first argument is 0,
	all entries are shown.

	Example: site syslog 5
	This shows all entries from 5 days ago to now.

	Example: site syslog 10 deleted
	This show all entries with "deleted" in from 10 days ago to now.

	Example: site syslog 0 1999
	This shows all entries with "1999" in from the start of the log to now.

site unnukes [#]
	Show recently unnuked releases (from "rootpath/datapath/logs/nukelog").

	Argument limits amount of shown releases, default 10.


 SITE-INFORMATION COMMANDS:
 ---------------------
site alias [string]
	Show current aliases. If an argument is provided, it will give the path
	where the argument is an alias for if it's an existing alias.

site cdpath
	Show current cdpaths.

site help [string]
	Display helpscreen.
	If a sitecommand is given as argument, it's syntax is shown.

site stat
	Display your current status line, definable in
	"rootpath/datapath/text/statline.txt".

site time
	Display current time on the site.

site vers
	Show current glFTPd version.

site welcome
	Display the welcome screen.
	See the "welcome_msg" setting in config file.


 USERSETTINGS COMMANDS:
 ----------------------
site color ["on"|"off"|"show"]
	Enables, disables or shows the current mode for having colors in
	listings and other serverreplies. Depends however on the "color_mode"
	setting in config.

	If no arguments are specified, this will toggle colormode.

	Note: this will screw up many Windows FTP clients if enabled.

site flags [user]
	Display the logged in or given user's flags, definable in
	"rootpath/datapath/text/flags.txt".

	Example: site flags
	This will show the logged in user's flags.

	Example: site flags Archimede
	This will show flags of user "Archimede".

site group [group]
	Shows the logged in user's current public groups or joins/parts a group.
	All groups must be parted before a new one can be joined, no slots must
	be available in the group.
	Only via 'site chgrp' can private groups be joined/parted.

	Example: site group
	This will display the groups the logged in user is in.

	Example: site group ftp
	With this the logged in user joins or parts "ftp".

site idle [#]
	Sets the idle time limit in seconds for this session only. If no
	argument is provided, the current idle time limit is shown and the max
	that can be set. Default idle time is 900 and default max is 7200.
	See 'site change user idle_time', the IDLER (I) flag and the "-t" and
	"-T" flags for the daemon for altering the defaults.

site passwd <string>
	Change password for the logged in user.
	See "site chpass" for info and restrictions.

site tagline [stringlist]
	Shows the logged in user's tagline or sets it to the given argument.
	Only printable symbols and no '!', '"' and '%' allowed.

site xdupe [#]
	Shows the current xdupe mode or sets it to the given argument.
	See "xdupe" setting in config file.

	Possible values are:
	  0 = disabled, the default.
	  1 = several filenames per line divied by a space, up to 66 symbols;
	      filenames longer than 66 symbols are truncated.
	  2 = one filename per line; filenames londer than 66 are truncated.
	  3 = one filename per line; filenames are not truncated.

	Xdupe will allow clients that keep queues of files to be uploaded to
	quickly remove files from their queue if they already exist in the
	upload directory, without the need to refresh. This should minimize the
	number of "dupe"' errors as clients try to upload files that have
	already been uploaded by someone else to the current directory. When
	this is turned on, glFTPd will give a list of files, preceded by the
	string "X-DUPE: ", right before the usual "this file is a dupe" message.
	The files are first matched against a list of file masks on the "xdupe"
	line in glftpd.conf. See the included x-dupe-info.txt for more details.
	Known client to support this is pftp.


 FILE & DIRECTORY COMMANDS:
 ------------------------
site chmod <#> <path|filepath>
	Changes a directory's or file's permissions to the first argument. This
	should be a number in octal form ranging from 000 to 777.

	Example: site chmod 444 file.zip
	This makes "file.zip" in the current directory read-only.

	Example: site chmod 777 /site/incoming
	This makes the "/site/incoming" directory read and writeable by anyone.

site nuke <path|dir> <#> <stringlist>
	Nukes the first argument being a directory, either absolute or relative.
	If the directory contains spaces, use '{' and '}' around it.

	The second argument is the multiplier, which removes an extra amount of
	credits from the nukees. Total amount of credits removed follows this
	scheme: (ratio + multiplier - 1) x uploaded bytes. If the multiplier or
	ratio is 0, then no credits are removed at all.
	Also see the "multiplier_max" setting in the config file.

	The third argument is the reason for the nuke.

	Example: site nuke shit 2 CRAP
	This will nuke the directory "shit" with multiplier 2 and reason "CRAP".

	Example: site nuke {/incoming/A dir with spaces} 0 test
	This nukes "/incoming/A dir with spaces" which is an absolute path
	with spaces relative to the nuker's $HOME, with no punishment (nukees
	keep al gained credits) and reason "test".

site unnuke <path|dir> [stringlist]
	Unnukes the first argument being a directory, either absolute or
	relative, without the possible prefix and/or suffix thatcan be defined
	by the "nukedir_style" setting's first argument.
	If the directory contains spaces, use '{' and '}' around it.
	Also see the "nukedir_style" setting to allow unnuking.

	The second argument is the reason for the unnuke, by default "None".

	Example: site unnuke shit NOT CRAP
	This will unnuke the directory "shit" with reason "NOT CRAP".

site wipe [-"r"] <path|filepath|file|dir>
	BE CAREFUL TO WHO ACCESS TO THIS COMMAND IS GIVEN.

	This is the same as deleting a file, except the fileowner doesn't lose
	credits and upload stats.

	If the argument is a file, it will simply be deleted. If it's a
	directory, it and the files it contains will be deleted. If the
	directory contains other directories, the deletion will be aborted.
	Symlinks are treated like normal files, they are not followed.

	To remove a directory containing subdirectories, the optinal argument
	needs to be used. The parent directory of the file/directory being wiped
	will be checked for owner write permissions, otherwise wipe will abort.

	Delete permission is needed, see "delete" permission in config file.

	Wiped directories are also removed from the dirlog database, wiped files
	however are not removed fro mthe file dupe database.
	

 OTHER COMMANDS:
 ---------------
site emulate <user>
	Load user's userfile into your process' memory, essentially becoming
	that user (although some things, like home directory or 'site who'
	display, won't change). Needs -emulate permission in config file.
	This was created for special scripts, so they can act as some user
	without having to know the password and logging in as that user. Most
	siteops will find no use for this.

	To stop emulating, either logout or 'site emulate YOU'.

	Note: this isn't TRUE emulation and shouldn't be used to do serious
	things. Some things won't work, and some will work incorrectly. The
	only way to achieve 'true' emulation is to log in as that user.

site give <user> <#>["m"|"M"|"g"|"G"] [stringlist]
	Gives credits equal to the second argument to user, taking these from
	the logged in user. Amount is in KiB, use the suffixes for MiB or GiB.
	Last argument is a message to be sent.

	Example: site give Archimede 100000 there you go
	This will give 100000 KiB of credits to user "Archimede" and send the
	message "there you go".

site kick <user>
	Kick a user off the site.

	Example: site kick Archimede
	This will kill all connections for the user "Archimede"

site kill <#>
	Kill a PID belonging to a glFTPd user, obtainable via 'site swho'.

	Example: site kill 345
	This will kill PID #345 (if it belongs to a glFTPd user).

site msg <"read"|"save"|"purge"|"tread">
site msg <user> <stringlist>
site msg <=group> <stringlist>
site msg <{> <user> [user ...] <}> <stringlist>
site msg <*> <stringlist>
	Every user has a mailbox with an inbox and a trashbin. With the first
	command, a user can read and manage it's messages. With the other
	commands, a user can send a message to other users.

	With "read", messages in the inbox are shown and moved to the trashbin.
	Use "save" to return messages from the trashbin to the inbox.
	To see which messages are in the trashbin, "tread" can be used.
	And finally the trashbin can be emptied with "purge", this also happens
	when the user logs out (not when the connection times out).

	The other commands respectively send a message to the given user, all
	members of the given group, all users listed (max 30) or all users.

	Example: site msg Archimede Hi there!
	This will send the message "Hi there!" to user "Archimede".

	Example: site msg =STAFF don't add any more users.
	This will send the message "don't add any more users." to every member
	of group "STAFF".

	Example: site msg { fran bob john mary } whats up
	This will send the message "whats up" to users fran, bob, john and mary.

site onel [stringlist]
	Shows the current oneliners or adds the given argument as oneliner.
	See the "oneliners" setting in the config file.

site request [stringlist]
	Shows the current requests or adds the given argument as request.
	See the "requests" setting in the config file.

	Added requests are also logged in "rootpath/datapath/logs/request.log".

	Example: site request A new super-duper-fast computer!
	This will add the logged in user's request to the requestlist.

site reqfilled <#>
	Fill a request by giving the number as found via 'site request'.
	See the "requests" setting in the config file.

	Filled requests are also logged in "rootpath/datapath/logs/request.log".

	Example: site reqfilled 3
	This will remove request number 3 from the request list and send mail to
	the user who made the request saying it was filled by who.

site swho
	Shows detailed information about users online.

site take <user> <#>["m"|"M"|"g"|"G"] [stringlist]
	Takes credits equal to the second argument from user; credits are NOT
	given to the logged in user. Amount is in KiB, use the suffixes for MiB
	or GiB. Last argument is a message to be sent.

	Example: site take Archimede 100000 haha
	This will take 100000 KiB of credits from user "Archimede" and send the
	message "haha".

site who
Display users currently online.
