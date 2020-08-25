# grouper-to-github

A python script that creates GitHub repositories and sets repo authorization
based on the membership of a Grouper group (or a list of CalNet usernames).

Requires:
  * an existing grouper group (optional)
  * grouper credentials (optional)
  * an existing github organization
  * github credentials

## Notes

If one wants to use a CalGroup, this is the default (setting -g to True). If one only wants to use a comma-separated list list of CalNet IDs, set -g to False and simply use -u.

One can create a CalGroup for a course via https://github.com/ryanlovett/sis-to-calgroups. This uses Travis to automatically create CalGroup groups whenever a course is put into the courses/ subdirectory there. One needs the SIS class ID.

To determine the 'group' when populating the config file (see below), go to the CalGroup for the course to the top-level and click on 'more'. This will reveal the group ID, something like `edu:berkeley:org:stat:classes:2019-fall:24047:stat-classes-2019-fall-24047-all`. (Note that here 24047 is the SIS class ID.)

Need to request a github organization from campus via the GitHub Organization Request Form. See [here for a KB article](https://berkeley.service-now.com/kb?sys_kb_id=f5e5c489db237b4497e5d144ce9619af&id=kb_article_view&sysparm_rank=4&sysparm_tsqueryId=8bd70e5ddb2bb34458bbdbf0ce9619c9). Failing that, one can email github@berkeley.edu.

E.g., here's an example email: We'd like to setup a new organization for a course within github.b.e. The class is Stat 243 and the instructor is 'paciorek'. May we please have the organization "stat243-fall-2019" with owners paciorek and rylo?

Before running the script to provision the repositories, ask students to login one time to github.b.e with their CalNet credentials. This in some way activates their github.b.e account. grouper-to-github.py checks if this is activated and if not, it does not create the repository. Why? Because if the account is not activated, the user cannot be added to the repository, so one gets a repository the user can't access.

Important: by default all members can read all repositories. So all students could see other student repositories. To modify this globally for the org, go to https://github.berkeley.edu/organizations/<org_name>/settings/member_privileges and change default permissions from Read to None.

Give any GSIs admin access to the organization. One would presumably also include them in the `admins` in the config, though having them as an admin for the org may obviate the need for this.

One can provide CalNet user names (which can be in the form of @berkeley.edu email addresses as a comma-separated list) via the -u flag.

## Example files

Config file:
```javascript
{
	"github_base_uri": "https://github.berkeley.edu/api/v3",
	"grouper_base_uri": "https://calgroups.berkeley.edu/gws/servicesRest/json/v2_2_100",
	"orgs": {
		"some-class-org": {
			"admins": ["theinstructor"],
			"team": "all",
			"group": "edu:berkeley:org:dept:dept-classes:dept-classes-2188:dept-classes-2188-dept-123"
        }
    }
}
```

Secrets file:
```javascript
{
	"github_user": "some-github-user",
	"github_token": "abc123...",
	"grouper_user": "some-grouper-user",
	"grouper_pass": "Y0rGr00p3rP4ssW.d"
}
```

`grouper-to-github.py -c ./config.json -s ./secrets.json`

## Issues to consider (as of fall 2019)

We now have a flag, -u, for `grouper-to-github.py` that allows addition of users not in the CalGroup based on their CalNet username.

As Ryan pointed out, it would be more coherent to add people to the CalGroup, e.g.:

```
$ sis people -y 2019 -s fall -n <course_id> -i campus-uid -c enrolled | tee 2019-fall-<course_id>-enrolled-uid.txt
$ cat 2019-fall-24047-enrolled-uid.txt | pbcopy
```

Then in calgroups.berkeley.edu, go to the course's enrolled group and import the uids via copy/paste (Add members > import a list of members > Copy/paste a list of member IDs).

Alternatively, if we don't want a CalGroup, we can go directly from SIS to GH. The `sis` script, https://github.com/ryanlovett/sis-cli, can pull down student or instructor lists as campus-uid or email based on the SIS class ID, e.g. 

```
$ sis people -y 2019 -s fall -n 24047 -i campus-uid -c enrolled
```

I guess this calls for a script which consumes these IDs and does what calgroups-to-github does.
