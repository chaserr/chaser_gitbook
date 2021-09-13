# 打包脚本

## 使用方法

if \[ ! -d ./IPADir \]; then mkdir -p IPADir; fi

## 工程绝对路径

project\_path=$\(cd `dirname $0`; pwd\)

## 工程名 将XXX替换成自己的工程名

project\_name=XXX

## scheme名 将XXX替换成自己的sheme名

scheme\_name=XXX

## 打包模式 Debug/Release

development\_mode=Debug

## build文件夹路径

build\_path=${project\_path}/build

## plist文件所在路径

exportOptionsPlistPath=${project\_path}/exportTest.plist

## 导出.ipa文件所在路径

exportIpaPath=${project\_path}/IPADir/${development\_mode}

echo "Place enter the number you want to export ? \[ 1:app-store 2:ad-hoc\] "

read number while\(\[\[ $number != 1 \]\] && \[\[ $number != 2 \]\]\) do echo "Error! Should enter 1 or 2" echo "Place enter the number you want to export ? \[ 1:app-store 2:ad-hoc\] " read number done

if \[ $number == 1 \];then development\_mode=Release exportOptionsPlistPath=${project\_path}/exportAppstore.plist else development\_mode=Debug exportOptionsPlistPath=${project\_path}/exportTest.plist fi

echo '///-----------' echo '/// 正在清理工程' echo '///-----------' xcodebuild  clean -configuration ${development\_mode} -quiet \|\| exit

echo '///--------' echo '/// 清理完成' echo '///--------' echo ''

echo '///-----------' echo '/// 正在编译工程:'${development\_mode} echo '///-----------' xcodebuild  archive -workspace ${project\_path}/${project\_name}.xcworkspace  -scheme ${scheme\_name}  -configuration ${development\_mode}  -archivePath ${build\_path}/${project\_name}.xcarchive -quiet \|\| exit

echo '///--------' echo '/// 编译完成' echo '///--------' echo ''

echo '///----------' echo '/// 开始ipa打包' echo '///----------' xcodebuild -exportArchive -archivePath ${build\_path}/${project\_name}.xcarchive  -configuration ${development\_mode}  -exportPath ${exportIpaPath}  -exportOptionsPlist ${exportOptionsPlistPath}  -quiet \|\| exit

if \[ -e $exportIpaPath/$scheme\_name.ipa \]; then echo '///----------' echo '/// ipa包已导出' echo '///----------' open $exportIpaPath else echo '///-------------' echo '/// ipa包导出失败 ' echo '///-------------' fi echo '///------------' echo '/// 打包ipa完成 ' echo '///-----------=' echo ''

echo '///-------------' echo '/// 开始发布ipa包 ' echo '///-------------'

if \[ $number == 1 \];then

## 验证并上传到App Store

## 将-u 后面的XXX替换成自己的AppleID的账号，-p后面的XXX替换成自己的密码

altoolPath="/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/Frameworks/ITunesSoftwareService.framework/Versions/A/Support/altool" "$altoolPath" --validate-app -f ${exportIpaPath}/${scheme\_name}.ipa -u XXX -p XXX -t ios --output-format xml "$altoolPath" --upload-app -f ${exportIpaPath}/${scheme\_name}.ipa -u XXX -p XXX -t ios --output-format xml else

## 上传到Fir

## 将XXX替换成自己的Fir平台的token

fir login -T XXX fir publish $exportIpaPath/$scheme\_name.ipa

fi

exit 0

