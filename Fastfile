# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Multi-Target upload 教學
# https://blog.devgenius.io/how-to-use-fastlane-to-deploy-multiple-targets-to-testflight-xcode-ios-41e1b932ef3
# 全部Target: fastlane execute
# 特定Target: fastlane execute —-env TargetName

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane

default_platform(:ios)

### 登入appStoreConnect的帳密
ENV["FASTLANE_USER"] = "YOUR_APPLE_ID"
ENV["FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD"] = "YOUR_SPECIFIC_PASSWORD"

### 上傳所有Targets(有設定.env的)
desc "Upload env to TestFlight"
lane :execute do
  # 更新build number
  build_number_with_date
    
  # 未指定env則上傳所有Target
  if ENV['SCHEME'] == nil
    # 取得.env檔案列表
    schemeList = Dir.glob(".env.*")  
    schemeList.each do |file|
      # 加載.env覆蓋環境變數
      Dotenv.overload(file)
      # 執行傳進來的方法
      build_and_upload
    end
  else 
    build_and_upload
  end
end

### 建立ipa檔
desc "Create ipa"
lane :build do
  gym
end

### 上傳TestFlight
desc "Upload to App TestFlight"
lane :upload do
  upload_to_testflight(
    app_identifier: ENV['BUNDLE_IDENTIFIER'],
    ipa: "./fastlane/builds/#{ENV['SCHEME']}.ipa",
    skip_waiting_for_build_processing: true,
    )
end

### 建立ipa檔並上傳TestFlight
def build_and_upload
  build
  upload
end

### 增加Build Number，會一次更新所有Target的BuildNumber
def build_number_with_date
  # 取得build number  ex.20241005001
  build_number = get_build_number(xcodeproj: "YOUR_PROJECT_NAME.xcodeproj")
  # 取日期
  build_date = build_number[0..7]
  # 獲取今天日期
  date = Time.now.strftime("%Y%m%d")
  
  # 宣告預設 build_number_int
  build_number_int = 0    
  # 如果buld number 已經是今天，那就build number + 1
  if build_date == date
    # 取後3碼並轉為Int
    build_number_int = build_number[-3..-1].to_i
    build_number_int += 1
  else
    # 如果buld number 不是今天，那就是今天的第一build，設定為1
    build_number_int = 1
  end
  
  # 格式化為3碼，不足補0
  suffix = sprintf("%03d", build_number_int)
  
  # 拼接字串
  build_number = "#{date}#{suffix}"
    
  # 設定build Number
  increment_build_number(
    build_number: build_number,
    skip_info_plist: true
  )  
end
