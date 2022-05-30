- 遠端版本庫的管理主要分兩個層級，一種是遠端版本庫（remote repository），另一種是遠端分支（remote branch）。
- 簡單來說，遠端版本庫指的是「可以 clone」的版本庫。Git 允許設定 0 到多個，通常是指以下情境：
	- 0 個，這通常是邊緣人，或是還在實驗中沒打算分享的專案
	- 1 個，通常用在實作中央集權管理版本庫的情境，如：企業私有產品開發
	- 2 個以上，這是分散管理版本庫權限的情境，如：開源原始碼專案
- 遠端分支正如其名，它代表「遠端版本庫的分支」。遠端分支也是分支，跟一般分支還是有差異：
	- 它不能用 `git branch` 做新增、修改、刪除，而是要透過其他方法
	- 通常在協作的時候，會需要追蹤（tracking）分支的版本與本地分支差異，所以 Git 也提供了設定追蹤遠端分支的方法。
- ## 管理遠端版本庫
	- 使用 `git remote` 指令（不加任何參數）可以顯示目前有多少遠端版本庫：
		- ```
		  ❯ git remote
		  origin
		  ```
	- 一般來說，clone 既有專案的版本庫回來開發的情境下，是不用新增遠端版本庫的，除非是：
		- 先在本機建立版本庫後（`git init`），再做遠端版本庫設定
		- 使用 [Forking Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow) 流程進行開源程式碼協作
	- ### 新增遠端版本庫
		- 新增一個遠端版本庫，會需要兩個必要資訊，一個是 URL，只要有協作需求，都有辦法問到這個資訊；另一個則是要為這個 URL 取一個名字，Git 預設的遠端都會叫 `origin`，但也可以叫別的名字。
		- 以 Forking Workflow 為例，今天執行完 Fork 後，可以執行下面這個指令完成新增一個遠端版本庫叫 `fork`：
			- ```
			  ❯ git remote add fork git@github.com:myaccount/repo.git
			  - ❯ git remote
			  fork
			  origin
			  ```
			- 新增完可以使用 `git remote show` 查看更多資訊，它會跟遠端取得資訊並顯示如下：
			  
			  ```
			  ❯ git remote show fork
			  * remote fork
			  Fetch URL: git@github.com:myaccount/repo.git
			  Push  URL: git@github.com:myaccount/repo.git
			  HEAD branch: master
			  Remote branch:
			    master tracked
			  Local ref configured for 'git push':
			    master pushes to master (local out of date)
			  ```
			  
			  #+BEGIN_TIP
			  這裡會發現 Git 在擷取（fetch）與推送，還可以設定成不同的 URL，但還不清楚實際使用情境，因此常見的都是設定成一樣。
			  #+END_TIP
	- ### 修改遠端版本庫
		- 一般不大會有修改的情境，大多數砍掉重練反而比較容易解決。舉一個曾遇過的修改例子：HTTPS 想改成 SSH，這是過去 GitHub 曾有個調整是把 [HTTPS 的密碼驗證關閉](https://github.blog/changelog/2021-08-12-git-password-authentication-is-shutting-down/)，因此那陣子都建議其他人改用 SSH。
		  
		  ```
		  ❯ git remote set-url fork git@github.com:myaccount/repo.git
		  ```
	- ### 刪除遠端版本庫
		- 最後要說明的操作就是刪除遠端版本庫了。毫無意外，就是這麼簡單：
			- ```
			  ❯ git remote remove fork
			  ```
- ## 管理遠端分支
	- 遠端分支的樣子通常會長像這樣：
		- ```
		  origin/master
		  origin/main
		  ```
	- 遠端分支也是分支，所以是一個參照（refs），因此只要需要代入參照的指令，都可以拿遠端分支來使用，比方說：
		- ```
		  ❯ git log origin/master
		  ❯ git branch new-master origin/master
		  ❯ git rebase origin/master
		  ❯ git merge origin/master
		  ```
	- 遠端分支也會有新增、修改與刪除的行為。操作遠端分支的動作，都會交由 git pull  與 git push 兩個指令處理。
	- ### 追蹤遠端分支
		- Git 是分散式版本控制系統，有時候不同的分支會跟跟不同的上游分支互動，比方說：GitHub Forking Workflow 協作模型裡，就會有可能會應用到這個技巧。
		- ![https://git-scm.com/book/en/v2/images/integration-manager.png](https://git-scm.com/book/en/v2/images/integration-manager.png)
		  collapsed:: true
			- 圖片來源：[Pro Git 5.1 - Distributed Workflows](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows)
		- 上圖是 Git 官網提到的分散式工作流程的例子，假設我想對 [Spring Framework](https://github.com/spring-projects/spring-framework) 開源專案做貢獻時：
			- 我沒有開源專案版本庫的寫入權限，但有下載權限，因此先把它 clone 到本機並設遠端名稱為 `origin`
				- ```
				  ❯ git clone [git@github.com](mailto:git@github.com):spring-projects/spring-framework.git
				  ```
			- Fork 開源專案，我對於 fork 版本庫有完整的存取權限，先設定為 `fork`
				- ```
				  ❯ git remote add fork [git@github.com](mailto:git@github.com):my-account/spring-framework.git
				  ```
			- 因平常會需要跟原始開源專案同步最新程式，所以把本機 `main` 設定上游分支為 `origin/main`
				- ```
				    # 這可以不用做，clone 的當下預設就完成設定了
				    ❯ git branch --set-upstream-to=origin/main main
				    ```
			- 開發程式的時候，會上傳到自己有權限的 `fork` 版本庫
				- ```
				  ❯ git branch --set-upstream-to=fork/new-feature feature
				  ```
			- 剩下的流程就是 [GitHub Flow](https://docs.github.com/en/get-started/quickstart/github-flow) 的 Pull Request 流程，這邊就不贅述了
			- 設定追蹤後，在操作分支上會有什麼變化？主要有兩個：一個是在執行 git push 與 git pull 兩個指令的時候，預設目標會是追蹤分支。這相信可以節省很多打字量。
			- 另一個則是在執行 `git status` 指令時，會提醒本地分支版本與遠端分支版本的狀況，狀況有四種，含處理方法如下：
		- 同步（up to date）
		- 領先（ahead）幾個提交，領先指的是本地有，而遠端沒有的提交
			- 可以使用 git push 指令將本地提交更新到遠端
		- 落後（behind）幾個提交，落後指的是遠端有，而本地沒有的提交
			- 可以使用 git pull 指令把遠端提交更新到本地
		- 分歧（diverged），有領先 X 個提交，與落後 Y 個提交
			- 需要整合（integrate）版本，整合版本的方法可以參考整合版本的策略差異