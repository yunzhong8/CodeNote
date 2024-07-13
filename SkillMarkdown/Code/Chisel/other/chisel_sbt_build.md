```scala
// See README.md for license details.

ThisBuild / scalaVersion     := "2.13.12" //指定scala的版本
ThisBuild / version          := "0.1.0" 
ThisBuild / organization     := "com.github.yunzhong8"

val chiselVersion = "6.2.0" //用变量保存要使用chisel的版本

lazy val root = (project in file("."))
  .settings(
    name := "zzqChisel",
    libraryDependencies ++= Seq(
      "org.chipsalliance" %% "chisel" % chiselVersion, //设置chisel的版本
      "org.scalatest" %% "scalatest" % "3.2.16" % "test", //设置chiseltest的版本
    ),
    scalacOptions ++= Seq(
      "-language:reflectiveCalls",
      "-deprecation",
      "-feature",
      "-Xcheckinit",
      "-Ymacro-annotations",
    ),
    addCompilerPlugin("org.chipsalliance" % "chisel-plugin" % chiselVersion cross CrossVersion.full),
  )
```
