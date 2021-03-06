
require "pathname"
require "fileutils"

def system_or_exit(cmd, stdout = nil)
  puts "Executing #{cmd}"
  cmd += " >#{stdout}" if stdout
  system(cmd) or raise "******** Build failed ********"
end

## build ffmpeg

SDK_VERSION='8.1'

XCODE_PATH='/Applications/Xcode.app/Contents/Developer/Platforms'
GCC_PATH='/Applications/XCode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang'
LIB_PATH='/usr/lib/system'
GASPREP_DEST_PATH='/usr/local/bin'
PLATOFRM_PATH_SIM ='/iPhoneSimulator.platform'
PLATOFRM_PATH_IOS ='/iPhoneOS.platform'
SDK_PATH_SIM="/Developer/SDKs/iPhoneSimulator#{SDK_VERSION}.sdk"
SDK_PATH_IOS="/Developer/SDKs/iPhoneOS#{SDK_VERSION}.sdk"


FFMPEG_BUILD_ARGS_SIM = [
'--assert-level=2',
'--disable-mmx',
'--arch=i386',
'--cpu=i386',
"--extra-ldflags='-arch i386 -miphoneos-version-min=7.0'",
"--extra-cflags='-arch i386 -miphoneos-version-min=7.0'",
'--disable-asm',
]

FFMPEG_BUILD_ARGS_SIM_64 = [
'--assert-level=2',
'--disable-mmx',
'--arch=x86_64',
'--cpu=x86_64',
"--extra-ldflags='-arch x86_64 -miphoneos-version-min=7.0'",
"--extra-cflags='-arch x86_64 -miphoneos-version-min=7.0'",
'--disable-asm',
]

FFMPEG_BUILD_ARGS_ARMV7 = [
'--arch=arm',
'--cpu=cortex-a8',
'--enable-pic',
"--extra-cflags='-arch armv7 -miphoneos-version-min=7.0'",
"--extra-ldflags='-arch armv7 -miphoneos-version-min=7.0'",
"--extra-cflags='-mfpu=neon -mfloat-abi=softfp'",
'--enable-neon',
# '--disable-neon',
'--enable-optimizations',
'--disable-debug',
'--disable-armv5te',
'--disable-armv6',
'--disable-armv6t2',
'--enable-small',
]

FFMPEG_BUILD_ARGS_ARMV7S = [
'--arch=arm',
'--cpu=cortex-a9',
'--enable-pic',
"--extra-cflags='-arch armv7s -miphoneos-version-min=7.0'",
"--extra-ldflags='-arch armv7s -miphoneos-version-min=7.0'",
"--extra-cflags='-mfpu=neon -mfloat-abi=softfp'",
'--enable-neon',
# '--disable-neon',
'--enable-optimizations',
'--disable-debug',
'--disable-armv5te',
'--disable-armv6',
'--disable-armv6t2',
'--enable-small',
]

FFMPEG_BUILD_ARGS_ARM64 = [
'--arch=arm64',
# '--cpu=cortex-a9',
'--enable-pic',
"--extra-cflags='-arch arm64 -miphoneos-version-min=7.0'",
"--extra-ldflags='-arch arm64 -miphoneos-version-min=7.0'",
"--extra-cflags='-mfpu=neon -mfloat-abi=softfp'",
'--enable-neon',
# '--disable-neon',
'--enable-optimizations',
'--disable-debug',
'--disable-armv5te',
'--disable-armv6',
'--disable-armv6t2',
'--enable-small',
]

#Adding FLV made video work

FFMPEG_BUILD_ARGS = [
'--disable-everything',
#'--enable-decoder=mpeg4',
#'--enable-decoder=mpegvideo',
'--enable-decoder=mp3',
'--enable-decoder=vorbis',
#'--enable-decoder=aac',
'--enable-decoder=h264',
#'--enable-decoder=flv',
#'--enable-parser=aac',
#'--enable-parser=mpeg4video',
'--enable-parser=mpegaudio',
#'--enable-parser=mpegvideo',
#'--enable-parser=ac3',
#'--enable-parser=h261',
'--enable-parser=h264',
'--enable-parser=vorbis',
#'--enable-parser=vc1',
#'--enable-demuxer=mpegvideo',
#'--enable-demuxer=aac',
#'--enable-demuxer=m4v',
'--enable-demuxer=flv',
#'--enable-demuxer=mov',
'--enable-demuxer=h264',
'--enable-demuxer=matroska',
#'--enable-demuxer=vc1',
#'--enable-muxer=h264',
#'--enable-muxer=mpeg2video',
#'--enable-muxer=mp4', 
#'--enable-muxer=mov',
'--enable-protocol=file',
#'--enable-indev=v4l',
#'--enable-indev=v4l2',
'--disable-ffmpeg',
'--disable-ffplay',
'--disable-ffserver',
'--disable-ffprobe',
'--disable-doc',
'--enable-bzlib',
'--target-os=darwin',
'--enable-cross-compile',
#'--disable-encoders',
#'--enable-nonfree',
# '--enable-gpl',
'--enable-version3',
]

FFMPEG_LIBS = [
'libavcodec',
'libavformat',
'libavutil',
'libswscale',
'libswresample',
]

def mkArgs(platformPath, sdkPath, platformArgs)
	
	cc = '--cc=' + GCC_PATH
	as = ""
	sysroot = '--sysroot=' + XCODE_PATH + platformPath + sdkPath
#	extra = '--extra-ldflags=-L' + XCODE_PATH + platformPath + sdkPath + LIB_PATH
	extra = ""
	args = FFMPEG_BUILD_ARGS + platformArgs
	args << cc 
	args << as
	args << sysroot
	args << extra
	
	args.join(' ')
end

def moveLibs(dest)
	FFMPEG_LIBS.each do |x|
		FileUtils.move Pathname.new("FFmpeg/#{x}/#{x}.a"), dest		
	end
end

def ensureDir(path)

	dest = Pathname.new path
	if dest.exist?
		FileUtils.rm Dir.glob("#{path}/*.a")
	else
		dest.mkpath
	end

	dest
end

def buildArch(arch)

	case arch
	when 'i386'
		args = mkArgs(PLATOFRM_PATH_SIM, SDK_PATH_SIM, FFMPEG_BUILD_ARGS_SIM)
	when 'x86_64'
		args = mkArgs(PLATOFRM_PATH_SIM, SDK_PATH_SIM, FFMPEG_BUILD_ARGS_SIM_64)
	when 'armv7'
		args = mkArgs(PLATOFRM_PATH_IOS, SDK_PATH_IOS, FFMPEG_BUILD_ARGS_ARMV7)
	when 'armv7s'
		args = mkArgs(PLATOFRM_PATH_IOS, SDK_PATH_IOS, FFMPEG_BUILD_ARGS_ARMV7S)		
	when 'arm64'
		args = mkArgs(PLATOFRM_PATH_IOS, SDK_PATH_IOS, FFMPEG_BUILD_ARGS_ARM64)		
	else
		raise "Build failed: unknown arch: #{arch}"
	end
	
	dest = ensureDir('FFmpeg/' + arch)
	
	system_or_exit "cd FFmpeg; ./configure #{args}"
	system_or_exit "cd FFmpeg; make"	
	moveLibs(dest)	
	# Comment out the following line to see executables
	system_or_exit "cd FFmpeg; [ -f -.d ] && rm -- -.d; make clean"

end

def mkLipoArgs(lib)
	#{}"-create -arch armv7 armv7/#{lib}.a -arch armv7 armv7s/#{lib}.a -arch arm64 arm64/#{lib}.a -arch i386 i386/#{lib}.a -arch x86_64 x86_64/#{lib}.a -output universal/#{lib}.a"
	"-create -arch armv7 armv7/#{lib}.a -arch armv7 armv7s/#{lib}.a -arch arm64 arm64/#{lib}.a -output universal/#{lib}.a"
end

def mkLipoDebugArgs(lib)
	"-create -arch armv7 armv7/#{lib}.a -arch armv7 armv7s/#{lib}.a -arch arm64 arm64/#{lib}.a -arch i386 i386/#{lib}.a -arch x86_64 x86_64/#{lib}.a -output universal/#{lib}.a"
end

desc "check gas-preprocessor.pl"
task :check_gas_preprocessor do	

	found = false

	ENV['PATH'].split(':').each do |x|
		p = Pathname.new(x) + 'gas-preprocessor.pl'
		if p.exist? && p.writable?
			found = true
			break;
		end
	end

	unless found
    # See http://stackoverflow.com/questions/5056600/how-to-install-gas-preprocessor for more info.
    puts "Installing the gas-preprocessor to #{GASPREP_DEST_PATH}"
    
    FileUtils.move Pathname.new("gas-preprocessor/gas-preprocessor.pl"), Pathname.new(GASPREP_DEST_PATH)
  	system_or_exit "chmod +x #{GASPREP_DEST_PATH}/gas-preprocessor.pl"
    
    # raise "Build failed: first install gas-preprocessor.pl.\nSee http://stackoverflow.com/questions/5056600/how-to-install-gas-preprocessor for more info."
	end

end

desc "Clean ffmpeg"
task :clean_ffmpeg do
	system_or_exit "cd FFmpeg; [ -f -.d ] && rm -- -.d; make clean"
end

desc "Build ffmpeg i386 libs"
task :build_ffmpeg_i386 do	
	buildArch('i386')	
end

desc "Build ffmpeg x86_64 libs"
task :build_ffmpeg_x86_64 do	
	buildArch('x86_64')	
end

desc "Build ffmpeg armv7 libs"
task :build_ffmpeg_armv7 do	
	buildArch('armv7')	
end

desc "Build ffmpeg armv7s libs"
task :build_ffmpeg_armv7s do	
	buildArch('armv7s')	
end

desc "Build ffmpeg arm64 libs"
task :build_ffmpeg_arm64 do	
	buildArch('arm64')	
end

def universalBuild(debug)
	ensureDir('FFmpeg/universal')
	
	FFMPEG_LIBS.each do |x|
		args=""
		if (debug)
			args = mkLipoDebugArgs(x)
		else
			args = mkLipoArgs(x)
		end
		system_or_exit "cd FFmpeg; xcrun -sdk iphoneos lipo #{args}"
	end
	
	dest = ensureDir('libs/FFmpeg/')

	FFMPEG_LIBS.each do |x|
		FileUtils.move Pathname.new("FFmpeg/universal/#{x}.a"), dest
	end
end

desc "Build ffmpeg universal libs for debugging - incl simulator"
task :build_ffmpeg_universal_debug do	
	universalBuild(true)
end

desc "Build ffmpeg universal libs for release - no simulator"
task :build_ffmpeg_universal_release do	
	universalBuild(false)
end

## build libkxmovie

def cleanMovieLib(config)
	buildDir = Pathname.new 'tmp/build'	
  	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration #{config} -sdk iphoneos#{SDK_VERSION} clean SYMROOT=#{buildDir}"
	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration #{config} -sdk iphonesimulator#{SDK_VERSION} clean SYMROOT=#{buildDir}"  	
end

desc "Clean libkxmovie-debug"
task :clean_movie_debug do
	cleanMovieLib 'Debug'
end

desc "Clean libkxmovie-release"
task :clean_movie_release do
	cleanMovieLib 'Release'
end

desc "Build libkxmovie-debug"
task :build_movie_debug do
	buildDir = Pathname.new 'tmp/build'
	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Debug -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch armv7s"			
	FileUtils.move Pathname.new('tmp/build/Debug-iphoneos/libkxmovie.a'), Pathname.new('tmp/build/Debug-iphoneos/libkxmovie_armv7s.a')	

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Debug -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch arm64"			
	FileUtils.move Pathname.new('tmp/build/Debug-iphoneos/libkxmovie.a'), Pathname.new('tmp/build/Debug-iphoneos/libkxmovie_arm64.a')	

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Debug -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch armv7"		

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Debug -sdk iphonesimulator#{SDK_VERSION} build SYMROOT=#{buildDir} -arch x86_64"	
	FileUtils.move Pathname.new('tmp/build/Debug-iphonesimulator/libkxmovie.a'), Pathname.new('tmp/build/Debug-iphonesimulator/libkxmovie_x86_64.a')	

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Debug -sdk iphonesimulator#{SDK_VERSION} build SYMROOT=#{buildDir}"	
	system_or_exit "lipo -create -arch armv7 tmp/build/Debug-iphoneos/libkxmovie.a -arch armv7 tmp/build/Debug-iphoneos/libkxmovie_armv7s.a -arch arm64 tmp/build/Debug-iphoneos/libkxmovie_arm64.a -arch x86_64 tmp/build/Debug-iphonesimulator/libkxmovie_x86_64.a -arch i386 tmp/build/Debug-iphonesimulator/libkxmovie.a -output tmp/build/libkxmovie.a"
end

desc "Build libkxmovie-release"
task :build_movie_release do
	buildDir = Pathname.new 'tmp/build'
	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Release -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch armv7s"	
	FileUtils.move Pathname.new('tmp/build/Release-iphoneos/libkxmovie.a'), Pathname.new('tmp/build/Release-iphoneos/libkxmovie_armv7s.a')	

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Release -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch arm64"	
	FileUtils.move Pathname.new('tmp/build/Release-iphoneos/libkxmovie.a'), Pathname.new('tmp/build/Release-iphoneos/libkxmovie_arm64.a')	

	system_or_exit "xcodebuild -project kxmovie.xcodeproj -target kxmovie -configuration Release -sdk iphoneos#{SDK_VERSION} build SYMROOT=#{buildDir} -arch armv7"	

	system_or_exit "lipo -create -arch armv7 tmp/build/Release-iphoneos/libkxmovie.a -arch armv7 tmp/build/Release-iphoneos/libkxmovie_armv7s.a -arch arm64 tmp/build/Release-iphoneos/libkxmovie_arm64.a -output tmp/build/libkxmovie.a"	
end

def copyFiles(dest)
	FileUtils.move Pathname.new('tmp/build/libkxmovie.a'), dest		
	FileUtils.copy Pathname.new('libs/FFmpeg/libavcodec.a'), dest
	FileUtils.copy Pathname.new('libs/FFmpeg/libavformat.a'), dest
	FileUtils.copy Pathname.new('libs/FFmpeg/libavutil.a'), dest
	FileUtils.copy Pathname.new('libs/FFmpeg/libswscale.a'), dest
	FileUtils.copy Pathname.new('libs/FFmpeg/libswresample.a'), dest
	FileUtils.copy Pathname.new('kxmovie/KxMovieViewController.h'), dest	
	FileUtils.copy Pathname.new('kxmovie/KxAudioManager.h'), dest	
	FileUtils.copy Pathname.new('kxmovie/KxMovieDecoder.h'), dest
	FileUtils.copy_entry Pathname.new('kxmovie/kxmovie.bundle'), dest + 'kxmovie.bundle'
end

desc "Copy to output folder"
task :copy_movie_release do	
	dest = ensureDir 'output/release'	
	copyFiles(dest);
end	

desc "Copy to output folder"
task :copy_movie_debug do	
	dest = ensureDir 'output/debug'	
	copyFiles(dest);
end	

##
task :clean => [:clean_movie_debug, :clean_movie_release, :clean_ffmpeg]
task :build_ffmpeg => [:check_gas_preprocessor, :build_ffmpeg_armv7, :build_ffmpeg_armv7s, :build_ffmpeg_arm64, :build_ffmpeg_i386, :build_ffmpeg_x86_64, :build_ffmpeg_universal_debug]
task :build_ffmpeg_device_only => [:check_gas_preprocessor, :build_ffmpeg_armv7, :build_ffmpeg_armv7s, :build_ffmpeg_arm64, :build_ffmpeg_universal_release]
#task :build_movie => [:build_movie_debug, :copy_movie] 
task :build_movie => [:build_movie_release] 
task :build_all_release => [:clean, :build_ffmpeg_device_only, :build_movie, :copy_movie_release] 
task :build_all_debug => [:clean, :build_ffmpeg, :build_movie_debug, :copy_movie_debug] 
task :build_all => [:build_all_debug] 
task :default => [:build_all]
