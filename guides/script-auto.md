## Script: Auto

A script to demonstrate the automation of a release cycle using GitFlow branching strategy.

Create repo:
  * https://github.com/new
  * release-playground

Reset repo:

```
rm -rf .git
rm feature.txt
cat 0.1.0 > VERSION
git init
git add .
git commit -m "Seed 0.1.0"
git config --local credential.username "nicholasjhenry"
git remote add origin https://github.com/nicholasjhenry/release-playground.git
git branch -M main
git push -u origin main
```

Rename `main` => `develop`:
  * https://github.com/nicholasjhenry/release-playground/settings/branches
  * Repush main: `git push`

Enable pull-request actions:
  * https://github.com/nicholasjhenry/release-playground/settings/actions
  * "Allow GitHub Actions to create and approve pull requests"


```
git branch -m main develop
git fetch origin
git branch -u origin/develop develop
git remote set-head origin -a
git checkout main
git push
```

Add feature:

```
git checkout develop
echo 0.2.0-dev > VERSION
git commit -a -m "[RELEASE] 0.1.0 => 0.2.0-dev"
git push
git checkout -b feature/JIRA-01/add-feature
echo "Feature 0.2.0-dev" > feature.txt
git add feature.txt && git commit -m "Add feature"
echo "Refactor 0.2.0-dev" >> feature.txt
git add feature.txt && git commit -m "Refactor feature"
git push
gh pr create --title "[JIRA-01] Add feature" --base develop --fill
gh pr merge 1 --squash --subject "[JIRA-01] Add feature (#1)"
git checkout develop
git pull
```

Start release cycle (RC1):

https://github.com/nicholasjhenry/release-playground/actions/workflows/release-cycle_start.yml

Only release cycle started:

```
git checkout develop
echo 0.3.0-dev > VERSION
git commit -a -m "[RELEASE] 0.2.0-dev => 0.3.0-dev"
git push
```

Fix a bug in the release cycle:

```
git pull
git checkout release/0.2.0
git checkout -b fix/JIRA-02/fix-feature
echo "Fix 0.2.0-rc.1" >> feature.txt
git commit -a -m "Fix feature"
git push
gh pr create --title "[JIRA-02] Fix feature" --base release/0.2.0 --fill
gh pr merge 3 --squash --subject "[JIRA-02] Fix feature (#3)"
git checkout release/0.2.0
git pull
```

Continue release cycle (RC2):

https://github.com/nicholasjhenry/release-playground/actions/workflows/release-cycle_next-rc.yml

Final release:

https://github.com/nicholasjhenry/release-playground/actions/workflows/release-cycle_final.yml

Merge upstream:

```
git checkout develop
git merge v0.2.0^0 # Merge release 0.2.0 from main
git push
```


