# AppCenter - How to add access to partners

1. Create a new distribution group related to the app you want add access to (if already doesn't exists). 
Distribution group management can be found at adress https://appcenter.ms/orgs/futured/apps/app-name/distribute/distribution-groups 
(`app-name` should be replaced with a real name). 
2. Add partners' emails as Testers of this distribution group.
3. Add created distribution group to `$APP_CENTER_DISTRIBUTION_GROUPS` environment variable 
to `enterprise` workflow at Bitrise. Distribution groups should be comma separated, e.g. 
`"Collaborators,NewGroup"`. 
4. Enjoy.
