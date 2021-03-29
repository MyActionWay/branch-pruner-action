# <center>Branch-Pruner | GitHub Action</center>

<center>
<big>
CAUTION: IT IS A POWERFUL TOOL AND YOU USE IT AT YOUR OWN RISK. CUTS CAN'T BE UNDONE.

[[MIT License]](https://github.com/myactionway/branch-pruner-action/blob/master/LICENSE.txt "Go there")
</big>
</center>
<hr>

As [Sacha Willems posted](https://www.saschawillems.de/blog/2017/09/10/how-to-shrink-down-a-github-repository/ "Go there"): <i>"Shrinking a git(hub) repository isn’t just about deleting locally present files but requires cleaning up the history as files that have been removed are still present in the repository’s history and therefore still contribute to it’s size."</i>

With the GitHub action [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it"), you can easily reduce the size of a GitHub repository by manually and/or automatically truncating the old commit history of a selected branch. This means that you can delete all commits with previous and unused file versions up to an arbitrarily selected point in your Git history without losing newer commits with newer file versions of the selected branch tree.

Normally YOU SHOULD NEVER DO THIS and there are [huge drawbacks](#-drawbacks "Go there"). However, in some cases it is really useful to get rid of the old stuff on a regular basis. E.&nbsp;g., if your repository size is growing continuously and you only ever need the latest commit history. Or when you encounter problems of a general slowness with Git commands like `push` and `pull`. Then it's time for the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it"). It will speed you up again :wink:.

## | Credits

I, [Sitdisch](https://github.com/sitdisch "Visit me"), created the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") because I needed a GitHub action that would periodically auto-crop my repo size, and there was no action out there before. My solution approach is based on [this blog post](https://passingcuriosity.com/2017/truncating-git-history/ "Go there") by Thomas Sutton and [this blog post](https://www.cognizantsoftvision.com/blog/tips-for-improving-git-performance/ "Go there") by Alin Ruscior. Thanks to both.

## | Drawbacks

The [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") rewrites the entire commit history of the branch being pruned. The new history takes the branch-tree of the selected `new-first-commit`. That means all subsequent commits have the old order and be authored by the original sources.

But the Drawbacks are:
* in the `new-first-commit`, the files are marked as created
* all commits have new time stamps and commit-hashes
* all commits are committed by the selected `User` (default: `github-actions[bot]` )
* all forks and other branches have nothing to compare with the pruned branch anymore
* cuts can't be undone.

## | Setup

Oh, you're still here then let's do it.

1. add the [`branch-pruner.yml`](https://github.com/myactionway/branch-pruner-workflow/.github/workflows/branch-pruner.yml "Get it") workflow file to a repository
	* the path has to be `.github/workflows/branch-pruner.yml`
	* it doesn't have to be the repository you want to prune; e.&nbsp;g., you can simply [fork](https://github.com/myactionway/branch-pruner-workflow/fork "fork it") the `myactionway/branch-pruner-workflow` repository
2. create a new encrypted repository secret [[procedure]](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository "Learn how")
	* add the secret to the same repository where you added the workflow file
	* give the secret a name e.&nbsp;g. `BRANCH_PRUNER_TOKEN`
	* the value of the secret must be the value of the personal access token for the repository to be pruned
		* [procedure for creating a personal access token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token "Learn how")
		* select only the minimum scopes and permissions required for your use case e.&nbsp;g. repo and workflow
3. adapt your `branch-pruner.yml` file
	* for manual triggers
		* all you have to do is enter your secret name e.&nbsp;g. `BRANCH_PRUNER_TOKEN`
			```yml
			env:
			 # Token for all triggers
			 TOKEN: {{ secrets.BRANCH_PRUNER_TOKEN }}
			```
			* CONSIDER: never enter the actual value of the personal access token
		* [procedure for manually running a workflow](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-on-github "Learn how")
			* CONSIDER: currently, you can't change the token in the UI
	* for other triggers
		* adapt this section
			```yml
			##############################################################
			# DEFINE YOUR TOKEN, INPUTS AND TRIGGERS IN THE FOLLOWING
			##############################################################

			# TOKEN and INPUTS as environmental variables
			env:
			#	Token for all triggers
				TOKEN: #  e.g. {{ secrets.BRANCH_PRUNER_TOKEN }}
			#
			#	Inputs for not manually triggered workflows
				NEW-FIRST-COMMIT: # e.g. commit-hash or HEAD~N etc.
				REPOSITORY: # target repo e.g. 'dummy/mytargetrepo'
				BRANCH: # branch to be pruned e.g 'master'
				USER-NAME: # user who should commit e.g. 'dummy'
				USER-EMAIL: # e.g. 'dummy@gmail.com'

			# TRIGGERS
			on:
			#	push:
			#	schedule:
			#		- cron: '00 23 28 * *'
			```
		* CONSIDER:
			* token: never enter the actual value of the personal access token
			* inputs:
				* if any input is blank, one of these default values will be used instead
					```yml
					DEFAULT-REPOSITORY: ${{ github.repository }} # is the repo with this file
					DEFAULT-BRANCH: 'master'
					DEFAULT-USER-NAME: 'github-actions[bot]'
					DEFAULT-USER-EMAIL: '41898282+github-actions[bot]@users.noreply.github.com'
					```
				* choose your `new-first-commit` carefully; E.&nbsp;g., `HEAD~N` is really useful for autonomously truncating N old commits of a branch on a regular basis. However, know what you are doing. `HEAD~N` or `HEAD^N` may not be the commits you're targeting. For more information about HEAD~N and HEAD^ look e.&nbsp;g. [here](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git "Go there").
			* trigger-schedule:
				* e.&nbsp;g. `cron: '00 23 28 * *'` executes the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") every 28th day of a month at 23:00
				* you can check your inputs [here](https://crontab.guru/ "Go there")

## | Known issues & possible solutions

* workflow trigger `schedule` doesn't fire
	* in my experience, a workflow file with this trigger must be placed in the default branch
	* in this [chat](https://github.community/t/schedule-workflows-missing/17653/3 "Go there") Brightran said: <i>"... The workaround is to push something to trigger them. ..."</i> and Hless said: <i>"... It appears to me that it takes while before schedules actions run at all in a new repo"</i>. In my experience, they are right.
* this error `fatal: refusing to merge unrelated histories` occurs when you pull the pruned branch back to your local machine
	* possible solution [[source](https://stackoverflow.com/questions/1125968/how-do-i-force-git-pull-to-overwrite-local-files "Go there")]:
		1. `git fetch --all`
		2. `git reset --hard origin/<PRUNED_BRANCH>` (replace `<PRUNED_BRANCH>`)
