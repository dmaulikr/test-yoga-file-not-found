source 'https://github.com/CocoaPods/Specs.git'
# Uncomment this line to define a global platform for your project
platform :ios, '9.0'
REACT_NATIVE_PATH = './node_modules/react-native'

# https://github.com/facebook/react-native/issues/11272#issuecomment-268984452
def patch_react(installer)
  version = `node -p -e "require('#{REACT_NATIVE_PATH}/package.json').version"`
  puts ">> Patching React Native #{version}"

  # patch header files
  # https://github.com/facebook/react-native/issues/11272
  `find #{REACT_NATIVE_PATH} \\( -name *.h -o -name *.m \\) -print0 | xargs -0 sed -i '' -e 's:<yoga/\\(.*\\)>:"\\1":g'`
  `find #{REACT_NATIVE_PATH} \\( -name *.h -o -name *.m \\) -print0 | xargs -0 sed -i '' -e 's:<jschelpers/\\(.*\\)>:"\\1":g'`
  `find #{REACT_NATIVE_PATH} \\( -name *.h -o -name *.m \\) -print0 | xargs -0 sed -i '' -e 's:<cxxreact/\\(.*\\)>:"\\1":g'`

  # exclude duplicate symbols
  # https://github.com/facebook/react-native/issues/11502
  installer.pods_project.targets.each do |target|
    next unless target.name == 'React'

    source_files = target.source_build_phase.files
    uniqs = source_files.uniq { |f| f.file_ref.path }
    (source_files - uniqs).each { |dup|
      source_files.delete(dup)
    }
  end
end

target 'test' do
  # Comment this line if you're not using Swift and don't want to use dynamic frameworks
  use_frameworks!

  pod 'React', :path => './node_modules/react-native', :subspecs => [
    'Core',
    'RCTText',
    'RCTImage',
    'RCTWebSocket', # needed for debugging
    'RCTNetwork',
  ]
end

post_install do |installer|
  patch_react(installer)
end