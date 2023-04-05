## Script: Manual

A script to demonstrate the release cycle using GitFlow branching strategy (manual only).

Create repo:
  * https://github.com/new
  * release-playground

Reset repo:

```
git reset --hard v0.1.0
git branch | grep -v "main" | xargs git branch -D
git push
```

Rename `main` => `develop`:
  * https://github.com/nicholasjhenry/release-playground/settings/branches
  * Repush main: `git push`

Add feature:

```
git checkout develop
echo 0.2.0-dev > VERSION
git commit -a -m "Bump: 0.1.0 => 0.2.0-dev"
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

```
git checkout -b release/0.2.0
echo 0.2.0-rc.1 > VERSION
git commit -a -m "Bump: 0.2.0-dev => 0.2.0-rc.1"
git push
gh pr create --title "[RELEASE] 0.2.0" --base main --fill
git branch --set-upstream-to=origin/`git symbolic-ref --short HEAD` # track branch
git tag v0.2.0-rc.1
git push origin v0.2.0-rc.1
gh release create v0.2.0-rc.1 --title "0.2.0-rc.1" --generate-notes --prerelease

git checkout develop
echo 0.3.0-dev > VERSION
git commit -a -m "Bump: 0.2.0-dev => 0.3.0-dev"
git push
```

Fix a bug in the release cycle:

```
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

```
echo 0.2.0-rc.2 > VERSION
git commit -a -m "Bump: 0.2.0-rc.1 => 0.2.0-rc.2"
git push
git tag v0.2.0-rc.2
git push origin v0.2.0-rc.2
gh release create v0.2.0-rc.2 --title "0.2.0-rc.2" --generate-notes --prerelease
```

Final release:

```
echo 0.2.0 > VERSION
git commit -a -m "Bump: 0.2.0-rc.2 => 0.2.0"
git push
gh pr merge 2 --merge --subject "[RELEASE] 0.2.0" --body ""
git checkout main
git pull
git tag v0.2.0
git push origin v0.2.0
gh release create v0.2.0 --title "0.2.0" --generate-notes
```

Merge upstream:

```
git checkout develop
git merge v0.2.0^0 # Merge release 0.2.0 from main
git push
```


