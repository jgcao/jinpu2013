##Git开发规范命令

###个人项目开发

	git fetch origin develop:yourbranch //从develop上获取新的分支
	
	git checkout yourbranch //切换至此分支进行开发
	
	git add .
	
	git commit –m ‘your commit’ //提交代码至当前分支


###个人项目提交测试
	首先要提交code review，详情参考《reviewboard简易使用说明》
	申请一个测试控件 http://qaadmin.corp.anjuke.com/MyWork/Branch.php?mod=6 
	将开发好的项目推到申请的测试空间，这里有很多做法，殊途同归，这里推荐一套比较干净的做法：
	在测试空间上，重新获取你的分支的所有代码。
	cd /var/www/fp01/jinpu
	git branch master
	git checkout master
	git branch –D yourbranch   //切走当前分支，把可能存在的你的分支名先删除
	git fetch /var/www/yourfolder/jinpu/ yourbranch:yourbranch
	git checkout yourbranch
	cd /var/www/yourfolder/jinpu //回到你的开发路径

	这些可以参照/var/www/turtle/test.sh 写一个脚本，方便提交，第一个参数为分支名，第二个参数为测试空间名


###获取最新的代码
		开发过程中，可能会需要更新到最新的代码（比如线上已经修改过了bug，或者开发时间过长需要同步，或者在提交代码至develop之前），这时候可以这样：
		git fetch origin
		先把origin的资源库和你的资源库同步
		git rebase origin/develop
		rebase到最新的develop代码，然后把你所有的提交内容接在此之后，这个时候可能会产生冲突，需要进行解决：
		先根据提示，把有冲突的文件编辑好
		git add . //记住这个时候不用commit
		git rebase –continue //继续rebase

###合并代码至develop
	开发并且测试完毕后，需要在上线当日早上10点以前或前一日晚上将代码合并入develop
	参考上节“获取最新的代码”，将你的分支rebase到最新的develop
	没有冲突或者解决冲突就可以直接将代码推到develop
	git push origin develop:develop

###多人协作开发
	很多时候我们会多人协作开发一个项目，如前端和后端的合作，有时代码会互相依赖,操作如下：
	先由一个人从develop拉一个分支出来
	git fetch origin develop:cobranch
	然后将其推到origin上
	git push origin cobranch:cobranch
	其他直接从origin的cobranch上获取代码
	git fetch origin cobranch:cobranch
	所有人在自己获取的分支上开发，提交
	当需要依赖他人代码的时候，由对方先把代码推到origin的cobranch上，自己再从origin上rebase即可
	被依赖人：
	git fetch origin
	git rebase origin/cobranch
	git push origin cobranch:cobranch
	依赖人：
	git fetch origin
	git rebase origin/cobranch

	当需要最新develop代码的时候，由一个人rebase到最新的develop然后再推送到origin上，所有人再rebase即可。
	
	开发完毕后，逐个推送到origin的cobranch上，最后由一个负责人合并回develop即可。
	
	合作开发示意图：


###Bug修改
	需要立即上线的紧急bug(p1,p2)修改在master上修改。一般bug在develop上修改。
	git fetch origin develop:fixbug
	//修改bug
	git add .
	git commit –m ‘ibug xxxx’
	git push origin fixbug:develop

###备注
	通过pull或merge命令所产生的树形结构会比较复杂，所以大家操作的时候尽量不要使用此命令。
