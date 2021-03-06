# Launcher_source
only study source


现在 android 源码中 编译 Launcher3 模块,
然后 进入 /out/target/common/obj/APPS/Launcher3_intermediates/ 目录,

具体：Launcher3_intermediates/proto/src/com/android/launcher3/model/nano
修改gradle文件：

buildscript {

    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
        classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.2'
    }
    repositories {
        google()
        jcenter()
    }
}

apply plugin: 'com.android.application'
apply plugin: 'com.google.protobuf'

android {
    compileSdkVersion 26
    buildToolsVersion '26.0.2'

    defaultConfig {
        minSdkVersion 21
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"

//版本名后面添加一句话，意思就是flavor dimension 它的维度就是该版本号，这样维度就是都是统一的了
         flavorDimensions "versionCode"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        debug {
            minifyEnabled false
        }
    }

    productFlavors {
        aosp {
            applicationId 'com.android.launcher3'
            testApplicationId 'com.android.launcher3.tests'
        }
    }
    sourceSets {
        main {
            res.srcDirs = ['res']
            java.srcDirs = ['src', 'src_config']
            manifest.srcFile 'AndroidManifest-common.xml'
            proto.srcDirs 'protos/'
        }

        androidTest {
            java.srcDirs = ['tests/src']
            res.srcDirs = ['tests/res']
            manifest.srcFile "tests/AndroidManifest-common.xml"
        }

        aosp {
            manifest.srcFile "AndroidManifest.xml"
        }

        aospAndroidTest {
            manifest.srcFile "tests/AndroidManifest.xml"
        }
    }
}

repositories {
    google()
    jcenter()
}

final String SUPPORT_LIBS_VERSION = '26.0.0-alpha1'
dependencies {
    compile "com.android.support:support-v4:${SUPPORT_LIBS_VERSION}"
    compile "com.android.support:recyclerview-v7:${SUPPORT_LIBS_VERSION}"
    compile "com.android.support:palette-v7:${SUPPORT_LIBS_VERSION}"
    compile 'com.google.protobuf.nano:protobuf-javanano:3.0.0-alpha-2'

    testCompile 'junit:junit:4.12'
    androidTestCompile "org.mockito:mockito-core:1.+"
    androidTestCompile 'com.google.dexmaker:dexmaker:1.2'
    androidTestCompile 'com.google.dexmaker:dexmaker-mockito:1.2'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
    androidTestCompile "com.android.support:support-annotations:${SUPPORT_LIBS_VERSION}"
}

protobuf {
    // Configure the protoc executable
    protoc {
        artifact = 'com.google.protobuf:protoc:3.0.0-alpha-3'

        generateProtoTasks {
            all().each { task ->
                task.builtins {
                    remove java
                    javanano {
                        option "java_package=launcher_log.proto|com.android.launcher3.userevent.nano"
                        option "enum_style=java"
                    }
                }
            }
        }
    }
}
