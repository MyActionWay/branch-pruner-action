<div align="center">
<h1>Branch-Pruner | GitHub Action</h1>
<img src="https://repository-images.githubusercontent.com/352585266/cc34310b-3ab2-4085-b5f5-b1b2cc306a64"/>
<h3>CAUTION: IT IS A POWERFUL TOOL AND YOU USE IT AT YOUR OWN RISK. CUTS CAN'T BE UNDONE.</h3>
<img height="23" src="https://img.shields.io/github/repo-size/myactionway/branch-pruner-action?label=RepoSize&cacheSeconds=3600" />
<a title="Check it out" target="_blank" href="https://github.com/myactionway/branch-pruner-action/blob/main/LICENSE.txt"><img height="23" src="https://raw.githubusercontent.com/sitdisch/cloud/master/badges/particle/License-MIT.svg" /></a>
<a title="Check it out" target="_blank" href="https://github.com/MyActionWay/branch-pruner-action/releases"><img height="23" src="https://img.shields.io/github/v/release/myactionway/branch-pruner-action?label=LastRelease&cacheSeconds=3600" /></a><br>
[ <a title="Check it out" target="_blank" href="https://github.com/myactionway/branch-pruner-workflows">Workflow Readme</a> == <a title="Check it out" target="_blank" href="https://github.com/myactionway/branch-pruner-action">Action Readme</a> ]
</div><br><p></p>

As [Sacha Willems posted](https://www.saschawillems.de/blog/2017/09/10/how-to-shrink-down-a-github-repository/ "Go there"): <i>"Shrinking a git(hub) repository isn’t just about deleting locally present files but requires cleaning up the history as files that have been removed are still present in the repository’s history and therefore still contribute to it’s size."</i>

With the GitHub action [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it"), you can easily reduce the size of one/multiple GitHub repo(s) by manually and/or automatically truncating the old commit history of one/multiple selected branch(es). This means that you can delete all commits with previous and unused file versions up to an arbitrarily selected point in your Git history without losing newer commits with newer file versions of a selected branch tree.

Normally <b>YOU SHOULD NEVER DO THIS</b> and there are [huge drawbacks](#-drawbacks "Go there"). However, in some cases it is really useful to get rid of the old stuff on a regular basis. E.&nbsp;g., if your repository size is growing continuously and you only ever need the latest commit history. Or when you encounter problems of a general slowness with Git commands like `push` and `pull.` Then it's time for the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it"). It will speed you up again :wink:.

<br>

## | Credits

I, [Sitdisch](https://github.com/sitdisch "Visit me"), created the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") because I needed a GitHub action that would periodically auto-crop my repo size, and there was no action out there before. My solution approach is based on [this blog post](https://passingcuriosity.com/2017/truncating-git-history/ "Go there") by Thomas Sutton and [this blog post](https://www.cognizantsoftvision.com/blog/tips-for-improving-git-performance/ "Go there") by Alin Ruscior. Thanks to both.

P.S. my [Branch-Pruner Gif](https://repository-images.githubusercontent.com/352585084/3bae0d80-bb12-11eb-86c4-c420ef0fec71) based on the [Git Logo](https://git-scm.com/downloads/logos "Get it") by [Jason Long](https://twitter.com/jasonlong) [License: [CC&nbsp;BY&nbsp;3.0](https://creativecommons.org/licenses/by/3.0/)] and the scissor icon from the [googlefonts/noto-emoji](https://github.com/googlefonts/noto-emoji "Go there") repository [License: [Apache-2.0](https://github.com/googlefonts/noto-emoji/blob/main/LICENSE)].

<br>

## | Drawbacks

The [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") rewrites the entire commit history of the branch being pruned. The new history takes the branch-tree of the selected `NEW-FIRST-COMMIT.` That means all subsequent commits have the old order and be authored by the original sources.

But the Drawbacks are:
* the files are marked as created in the `NEW-FIRST-COMMIT`
* all commits have new time stamps and commit-hashes
* all commits are committed by the selected `User` (default: `github-actions[bot]`)
* all forks and other branches have nothing to compare with the pruned branch anymore
* cuts can't be undone.

<br>

## | Setups

Oh, you're still here then let's do it. First, choose a workflow file:

### [branch-pruner-easy.yml](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-easy.yml "Get it")
Truncates the old commit history of <b>the current main branch with minimal settings</b>.

<details><summary><b>Set it up (click to toggle)</b></summary>

<p>

> <details><summary><b>1. add the branch-pruner-easy.yml workflow file to a repository</b></summary>
> 
>	* [get the file](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-easy.yml "Get it")
> 
>	* it has to be the target repository where you want to prune the main branch (this is not the case with the other workflow files)
>	* the path has to be `.github/workflows/branch-pruner-easy.yml`
> 
> </details>

> <details><summary><b>2. create a new encrypted repository secret</b></summary>
> 
>	* [see how to do this in general](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository "Learn how")
>	
>	* give the secret a name e.&nbsp;g. `BRANCH_PRUNER_TOKEN`
>	* the value of the secret must be the value of the Personal Access Token (PAT) for the repository where you want to prune the main branch
>		* procedure for creating a [PAT (fine-grained)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-fine-grained-personal-access-token "Learn how") or a [PAT (classic)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic "Learn how")
>		
>		* select only the minimum scopes and permissions required
>			* PAT (fine-grained): repository permissions
>			
>				 * contents => access: read and write
>				 
>				 * metadata => access: read-only
>				 
>			* PAT (classic): e.&nbsp;g. repo and workflow
>			
>		* <b>CONSIDER</b>: [PAT expiration](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation) requires you to regenerate the PAT and set it as the secret's value again
>		
>	* add the secret to the same repository where you added this workflow file
> 
> </details>

> <details><summary><b>3. adapt your branch-pruner-easy.yml file</b></summary>
> 
> <p>
> 
> <details><summary><b>&nbsp;3.1 for manual triggers</b></summary>
>	
> 	* you don't have to adjust anything in the workflow file; just use it
> 	
> 		* [procedure for manually running a workflow on GitHub](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-on-github "Learn how")
> 		
> 			<img src="https://raw.githubusercontent.com/sitdisch/cloud/master/images/branchpruner_minimal_manual_inputs.png" />
> 		* [procedure for manually running a workflow using the GitHub CLI](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-github-cli)
> 		* [procedure for manually running a workflow using the REST API](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-the-rest-api)
> 
> </details>
> 
> <details><summary><b>&nbsp;3.2 for all other triggers</b></summary>
>	
> 	* adapt this section:
>		```yml
>		##############################################################
>		# DEFINE YOUR INPUTS AND TRIGGERS IN THE FOLLOWING
>		##############################################################
>
>		# INPUTS as environmental variables (env)
>		env:
>			NEW_FIRST_COMMIT: # e.g. commit-hash or HEAD~N etc.
>			TOKEN_NAME: # target token name e.g. 'BRANCH_PRUNER_TOKEN'
>		
>		# TRIGGERS
>		on:
>		#	push:
>		#	schedule:
>		#		- cron: '00 23 28 * *'
>		```
>		
>	  <b>CONSIDER</b>:
>		* INPUTS:
>		
>			* you only have to define `NEW_FIRST_COMMIT` and `TOKEN_NAME`;
>			
>			* `NEW-FIRST-COMMIT`: choose it carefully; E.&nbsp;g., `HEAD~N` is really useful for autonomously truncating commits on a regular basis. However, know what you are doing. `HEAD~N` or `HEAD^N` may be not the commits you're targeting. For more information about `HEAD~N` and `HEAD^N` look e.&nbsp;g. [here](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git "Go there").
>			* `TOKEN_NAME`: never enter the actual value of the personal access token
>		* TRIGGERS:
>			* `schedule`:
>				* e.&nbsp;g. `cron: '00 23 28 * *'` executes the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") every 28th day of a month at 23:00
>				
>				* you can check your inputs [here](https://crontab.guru/ "Go there")
>		* hidden defaults (changeable with the other workflow files):
>			* target repository & branch: repository with this workflow file and main branch
>			
>			* user settings:
>				* user who commit: github-actions[bot]
>				
>				* user e-mail address: 41898282+github-actions[bot]@users.noreply.github.com
> 
> <b>That's it. Happy pruning.</b>
>	
> </details>
>
> </p>
> 
> </details>

</p>

</details>

### [branch-pruner-default.yml](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-default.yml "Get it")
Truncates the old commit history of <b>a selected target branch</b>.

<details><summary><b>Set it up (click to toggle)</b></summary>

<p>

> <details><summary><b>1. add the branch-pruner-default.yml workflow file to a repository</b></summary>
> 
>	* [get the file](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-default.yml "Get it")
> 
> 	* it doesn't have to be the repository you want to prune; e.&nbsp;g., you can simply [fork](https://github.com/myactionway/branch-pruner-workflows/fork "fork it") the `myactionway/branch-pruner-workflows` repository
> 		* <b>CONSIDER</b>: with a forked repository, you need to confirm that you want to use a workflow before you can actually use it (repo menu > actions tab > push the button)
> 	* the path has to be `.github/workflows/branch-pruner-default.yml`
> 
> </details>

> <details><summary><b>2. create a new encrypted repository secret</b></summary>
> 
>	* [see how to do this in general](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository "Learn how")
>	
>	* give the secret a name e.&nbsp;g. `BRANCH_PRUNER_TOKEN`
>	* the value of the secret must be the value of the Personal Access Token (PAT) for the repository where you want to prune a branch
>		* procedure for creating a [PAT (fine-grained)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-fine-grained-personal-access-token "Learn how") or a [PAT (classic)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic "Learn how")
>		
>		* select only the minimum scopes and permissions required
>			* PAT (fine-grained): repository permissions
>			
>				 * contents => access: read and write
>				 
>				 * metadata => access: read-only
>				 
>			* PAT (classic): e.&nbsp;g. repo and workflow
>			
>		* <b>CONSIDER</b>: [PAT expiration](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation) requires you to regenerate the PAT and set it as the secret's value again
>		
>	* add the secret to the same repository where you added this workflow file
> 
> </details>

> <details><summary><b>3. adapt your branch-pruner-default.yml file</b></summary>
> 
> <p>
> 
> <details><summary><b>&nbsp;3.1 for manual triggers</b></summary>
>	
> 	* you don't have to adjust anything in the workflow file; just use it
> 	
> 		* [procedure for manually running a workflow on GitHub](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-on-github "Learn how")
> 		
> 			<img src="https://raw.githubusercontent.com/sitdisch/cloud/master/images/branchpruner_manual_inputs.png" />
> 		* [procedure for manually running a workflow using the GitHub CLI](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-github-cli)
> 		* [procedure for manually running a workflow using the REST API](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-the-rest-api)
> 
> </details>
> 
> <details><summary><b>&nbsp;3.2 for all other triggers</b></summary>
>	
> 	* adapt this section:
>		```yml
>		##############################################################
>		# DEFINE YOUR INPUTS AND TRIGGERS IN THE FOLLOWING
>		##############################################################
>
>		# INPUTS as environmental variables (env)
>		env:
>			NEW_FIRST_COMMIT: # e.g. commit-hash or HEAD~N etc.
>			TOKEN_NAME: # target token name e.g. 'BRANCH_PRUNER_TOKEN'
>			REPOSITORY: # target repository e.g. 'dummy/mytargetrepo'
>			BRANCH: # branch to be pruned e.g 'main'
>			USER_NAME: # user who should commit e.g. 'dummy'
>			USER_EMAIL: # e.g. 'dummy@gmail.com'
>		
>		# TRIGGERS
>		on:
>		#	push:
>		#	schedule:
>		#		- cron: '00 23 28 * *'
>		```
>	
>		<b>CONSIDER</b>:
>		* INPUTS:
>		
>			* you only have to define `NEW_FIRST_COMMIT` and `TOKEN_NAME`; if any other input is blank, one of these default values will be used instead
>				```yml
>				DEFAULT_REPOSITORY: ${{ github.repository }} # repo with this file
>				DEFAULT_BRANCH: 'main'
>				DEFAULT_USER_NAME: 'github-actions[bot]'
>				DEFAULT_USER_EMAIL: '41898282+github-actions[bot]@users.noreply.github.com'
>				```
>			* `NEW-FIRST-COMMIT`: choose it carefully; E.&nbsp;g., `HEAD~N` is really useful for autonomously truncating commits on a regular basis. However, know what you are doing. `HEAD~N` or `HEAD^N` may be not the commits you're targeting. For more information about `HEAD~N` and `HEAD^N` look e.&nbsp;g. [here](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git "Go there").
>			
>			* `TOKEN_NAME`: never enter the actual value of the personal access token
>		* TRIGGERS:
>			* `schedule`:
>				* e.&nbsp;g. `cron: '00 23 28 * *'` executes the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") every 28th day of a month at 23:00
>				
>				* you can check your inputs [here](https://crontab.guru/ "Go there")
> 
> <b>That's it. Happy pruning.</b>
>	
> </details>
>
> </p>
> 
> </details>

</p>

</details>

### [branch-pruner-advanced.yml](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-advanced.yml "Get it")
Truncates the old commit history of <b>multiple selected target branches</b>.

<details><summary><b>Set it up (click to toggle)</b></summary>

<p>

> <details><summary><b>1. add the branch-pruner-advanced.yml workflow file to a repository</b></summary>
> 
>	* [get the file](https://github.com/MyActionWay/branch-pruner-workflows/blob/main/.github/workflows/branch-pruner-advanced.yml "Get it")
>	
>	* it doesn't have to be a repository where you want to prune branches; e.&nbsp;g., you can simply [fork](https://github.com/myactionway/branch-pruner-workflows/fork "fork it") the `myactionway/branch-pruner-workflows` repository
>		* <b>CONSIDER</b>: with a forked repository, you need to confirm that you want to use a workflow before you can actually use it (repo menu > actions tab > push the button)
>	* the path has to be `.github/workflows/branch-pruner-advanced.yml`
>	
> </details>

> <details><summary><b>2. create new encrypted repository secrets</b></summary>
> 
>	* [see how to do this in general](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository "Learn how")
>	
>	* give the secrets names e.&nbsp;g. `BRANCH_PRUNER_TOKEN_1` and `BRANCH_PRUNER_TOKEN_2`
>	* the values of the secrets must be the values of the Personal Access Tokens (PAT) for the repositories where you want to prune branches
>		* procedure for creating a [PAT (fine-grained)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-fine-grained-personal-access-token "Learn how") or a [PAT (classic)](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic "Learn how")
>		
>		* select only the minimum scopes and permissions required
>			* PAT (fine-grained): repository permissions
>			
>				 * contents => access: read and write
>				 
>				 * metadata => access: read-only
>				 
>			* PAT (classic): e.&nbsp;g. repo and workflow
>			
>		* <b>CONSIDER</b>: [PAT expiration](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation) requires you to regenerate the PAT and set it as the secret's value again
>		
>	* add the secrets to the same repository where you added this workflow file
>
> </details>

> <details><summary><b>3. adapt your branch-pruner-advanced.yml file</b></summary>
> 
> <p>
> 
> <details><summary><b>&nbsp;3.1 define your defaults</b></summary>
>	
> 	* adapt this section:
>		```yml
>		##############################################################
>		# DEFINE YOUR DEFAULTS (INPUTS & TRIGGERS) IN THE FOLLOWING
>		##############################################################
>
>		# INPUTS as environmental variables (env)
>		env:
>			TOKEN_NAME: # target token name e.g. 'BRANCH_PRUNER_TOKEN_1'
>			REPOSITORY: # target repository e.g. 'dummy/mytargetrepo_1'
>			USER_NAME: # user who should commit e.g. 'dummy'
>			USER_EMAIL: # e.g. 'dummy@gmail.com'
>
>		# TRIGGERS
>		on:
>		#	push:
>		#	schedule:
>		#		- cron: '00 23 28 * *'
>			workflow_dispatch:
>		```
>		
>		<b>CONSIDER</b>:
>		* INPUTS:
>		
>			* `TOKEN_NAME`: never enter the actual value of the personal access token
>			
>			* all inputs except `TOKEN_NAME` have predefined values; you can, but you don't have to overwrite them
>				```yml
>				# Predefined values
>				REPOSITORY: ${{ github.repository }} # repo with this file
>				USER_NAME: 'github-actions[bot]'
>				USER_EMAIL: '41898282+github-actions[bot]@users.noreply.github.com'
>				```
>		* TRIGGERS:
>			* `schedule`:
>				* e.&nbsp;g. `cron: '00 23 28 * *'` executes the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") every 28th day of a month at 23:00
>				
>				* you can check your inputs [here](https://crontab.guru/ "Go there")
>			* `workflow_dispatch`:
>				* no predefined inputs; the `env` defined in this workflow file are used instead when this trigger is triggered
>				
>				* [procedure for manually running a workflow on GitHub](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-on-github "Learn how")
>				* [procedure for manually running a workflow using the GitHub CLI](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-github-cli)
>				* [procedure for manually running a workflow using the REST API](https://docs.github.com/en/actions/managing-workflow-runs/manually-running-a-workflow#running-a-workflow-using-the-rest-api)<p></p>
> 
> </details>
> 
> <details><summary><b>&nbsp;3.2 define your settings for the different target branches</b></summary>
>	
> 	* adapt this section:
>		```yml
>		##############################################################
>		# FIRST TARGET BRANCH | DEFINE YOUR ENV IN THE FOLLOWING
>		##############################################################
>		-	NAME: 'Pruning Branch 1'
>			NEW_FIRST_COMMIT: 'HEAD~40'
>			BRANCH: 'main'
>		#	TOKEN_NAME:
>		#	REPOSITORY:
>		#	USER_NAME:
>		#	USER_EMAIL:
>		##############################################################
>		# SECOND TARGET BRANCH | DEFINE YOUR ENV IN THE FOLLOWING
>		##############################################################
>		-	NAME: 'Pruning Branch 2'
>			NEW_FIRST_COMMIT: 'HEAD^20'
>			BRANCH: 'dev'
>		#	TOKEN_NAME: # e.g. 'BRANCH_PRUNER_TOKEN_2'
>		#	REPOSITORY: # e.g. 'dummy/mytargetrepo_2'
>		#	USER_NAME:
>		#	USER_EMAIL:
>		##############################################################
>		# THIRD TARGET BRANCH | FEEL FREE TO ADD MORE TARGET BRANCHES
>		# ...
>		```
>		<b>CONSIDER</b>:
>		
>		* you just have to define `NAME`, `NEW_FIRST_COMMIT` and `BRANCH` for each target branch; if you do not define any of the other inputs, your predefined defaults will be used instead
>		
>		* only a maximum of <b>256 target branches</b> per workflow run is possible [[GitHub restriction](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstrategymatrix "Go there")]
> 
> <b>That's it. Happy pruning.</b>
>	
> </details>
>
> </p>
> 
> </details>

</p>

</details>

<br>

## | Known issues & possible solutions

> <details><summary><b>The error <i>"fatal: refusing to merge unrelated histories"</i> occurs when you pull the pruned branch back to your local machine:</b></summary>
>
>	* possible solution [[source](https://stackoverflow.com/questions/1125968/how-do-i-force-git-pull-to-overwrite-local-files "Go there")]:
>	
>		1. `git fetch --all`
>		
>		2. `git reset --hard origin/<PRUNED_BRANCH>` (replace `<PRUNED_BRANCH>`)
> 
></details>

> <details><summary><b><i>"Error: fatal: could not read Username for '<span>h</span>ttps://github.com': terminal prompts disabled":</i></b></summary>
> 
> <p>
> 
> * your [personal access token may has expired](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation) and you need to set a new one as the value of the encrypted repository secret; that means back to the [setup section](#-setups "Go there")
> 
> * more information about this GitHub action checkout issue can be found e.&nbsp;g. [here](https://github.com/actions/checkout/issues/664)
> 
> </p>
> 
> </details>

> <details><summary><b><i>"remote: Permission to ... denied to ... fatal: unable to access '<span>h</span>ttps://github.com/...': The requested URL returned error: 403":</i></b></summary>
> 
> <p>
> 
> * your personal access token used does not have the minimum scopes/permissions required to add the Lighthouse results to your target repository
> 
> </p>
> 
> </details>

> <details><summary><b>The workflow logs do not provide enough detail to diagnose why a workflow, job, or step is not working as expected:</b></summary>
> 
> <p>
> 
> * enable [addition debug logging](https://docs.github.com/en/actions/managing-workflow-runs/enabling-debug-logging)
> 
> </p>
> 
> </details>

> <details><summary><b>You are experiencing strange behavior from GitHub actions:</b></summary>
> 
> <p>
> 
> * maybe it's a general incident [[status check](https://www.githubstatus.com/ "Check it")]
> 
> </p>
> 
> </details>

> <details><summary><b>Your workflow trigger schedule doesn't fire:</b></summary>
> 
> * in my experience, a workflow file with this trigger must be placed in the default branch
> 
> * in this [chat](https://github.community/t/schedule-workflows-missing/17653/3 "Go there") Brightran said: <i>"... The workaround is to push something to trigger them. ..."</i> and Hless said: <i>"... It appears to me that it takes while before schedules actions run at all in a new repo"</i>. In my experience, they are right.
> 
> </details>

<br>

## | Application example

For my <a href="https://github.com/mythemeway" title="Explore this" target="_blank"><img align="top" src="https://avatars.githubusercontent.com/u/52597090?s=60&amp;v=4" alt="@MyThemeWay" size="25" height="25" width="25" class="avatar "/></a> [Website-Boilerplates](https://github.com/mythemeway "Go there"), I use the [Lighthouse-Badger](https://github.com/myactionway/lighthouse-badger-action "Get it") :badger: :tokyo_tower: :medal_military: to update automatically my Lighthouse badges and reports once a week. Meanwhile, my repository size continues to grow.

To counter this, I use the [Branch-Pruner](https://github.com/myactionway/branch-pruner-action "Get it") once a month. That way, I have the repo size under control and also the ability to see the latest history of my badges and reports without the really old stuff.

<br>

## | Appendix

### Note on protected brand names and logos
> * The use of protected brand names, trade names, utility models and brand logos on this website does not constitute an infringement of copyright; rather, it serves as an illustrative note. Even if this is not marked as such at the respective points, the corresponding legal provisions always apply.
> 
> * The brand names and logos used are the property of their respective owners and are subject to their copyright provisions.
> * This offer is in no way related to the legal entities of the protected brand names and logos used.

### Note on liability for links
> * This README contains links to external third-party websites. The README operator has no influence on the content of these sites. Therefore, he cannot assume any liability. Instead, the respective provider is always responsible for the content.
> 
> * The linked pages were checked for possible legal violations at the time of linking and illegal content wasn't discernible. A permanent control of the linked pages is unreasonable without concrete evidence of an infringement. However, if the README operator becomes aware of such a violation, he will act immediately. 

### Readme uses:
> * [Shields.io](https://github.com/badges/shields "Check it out") [License: [CC0 1.0](https://github.com/badges/shields/blob/master/LICENSE "Go there")] 
