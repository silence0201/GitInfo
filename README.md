# GitInfo
GitInfo For iOS Dev


### 获取git的相关信息

Shell

```
#最后一次提交的SHA
git_version=$(git log -1 --format="%h")

#当前的分支
git_branch=$(git symbolic-ref --short -q HEAD)

#最后一次提交的作者
git_last_commit_user=$(git log -1 --pretty=format:'%an')

#最后一次提交的时间
git_last_commit_date=$(git log -1 --format='%cd' --date=short)

#编译时间
build_time=$(date '+%Y-%m-%d %H:%M:%S')


info_plist=${PROJECT_DIR}/${INFOPLIST_FILE}

/usr/libexec/PlistBuddy -c "Set :'CommitSHA'       '${git_version}'"                "${info_plist}"
/usr/libexec/PlistBuddy -c "Set :'CommitBranch'    '${git_branch}'"                 "${info_plist}"
/usr/libexec/PlistBuddy -c "Set :'CommitUser'      ${git_last_commit_user}"       "${info_plist}"
/usr/libexec/PlistBuddy -c "Set :'CommitDate'      '${git_last_commit_date}'"       "${info_plist}"
/usr/libexec/PlistBuddy -c "Set :'BuildTime'          '${build_time}'"                 "${info_plist}"
```

代码中获取:

```
NSString *commitSHA = [NSString stringWithFormat:@"最后提交SHA：%@", [infoDic objectForKey:CommitSHA]];
NSString *commitBranch = [NSString stringWithFormat:@"当前所在分支：%@", [infoDic objectForKey:CommitBranch]];
NSString *commitUser = [NSString stringWithFormat:@"最后提交用户：%@", [infoDic objectForKey:CommitUser]];
NSString *commitDate = [NSString stringWithFormat:@"最后提交时间：%@", [infoDic objectForKey:CommitDate]];
NSString *buildTime = [NSString stringWithFormat:@"本次编译时间：%@", [infoDic objectForKey:BuildTime]];
```


### 自动添加build版本:

```
if [ $CONFIGURATION == Release ]; then
echo "Bumping build number..."
plist=${PROJECT_DIR}/${INFOPLIST_FILE}

#自动增加
buildnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${plist}")
if [[ "${buildnum}" == "" ]]; then
echo "No build number in $plist"
exit 2
fi

buildnum=$(expr $buildnum + 1)
/usr/libexec/Plistbuddy -c "Set CFBundleVersion $buildnum" "${plist}"
echo "Bumped build number to $buildnum"

else
echo $CONFIGURATION " build - Not bumping build number."

fi
```