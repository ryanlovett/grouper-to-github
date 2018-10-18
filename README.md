# grouper-to-github

A python script that creates GitHub repositories and sets repo authorization
based on the membership of a Grouper group.

Requires:
  * an existing grouper group
  * grouper credentials
  * an existing github organization
  * github credentials

## Example

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
