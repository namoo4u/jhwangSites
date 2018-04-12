+++
date = "2016-12-10T15:04:27+09:00"
title = "lazybones 에 새로운 Template추가하기"
tags = [
  "groovy",
  "programming",
  "lazybones",
]
categories = [
  "programming",
]

+++

### lazybones 의 템플릿
lazybones 의 기본 템플릿은 list 를 통해서 볼 수 있는데, 이외의 다른 템플릿을 추가하기 위해서는 config.groovy에 새로운 Repository 를 추가해 줘야 한다.
```
lazybones list
```

${USER_HOME}/.lazybones/config.groovy
```
bintaryRepositories = [
  "kyleboon/lazybones",
  "griffon/griffon-lazybones-templates",
  "pledbrook/lazybones-templates"
]
```

griffon javafx 를 사용하는 groovy Sample 프로젝트를 생성하기 위해서는 아래와 같이 하면 된다.
```
lazybones create griffon-javafx-groovy griffon-javafx-groovy-sample
```


#### lazybones 의 추가 템플릿들
```
Available templates in kyleboon/lazybones

    dropwizard
    groovy-app
    java-basic
    jbake

Available templates in griffon/griffon-lazybones-templates

    griffon-javafx-groovy
    griffon-javafx-java
    griffon-javafx-kotlin
    griffon-lanterna-groovy
    griffon-lanterna-java
    griffon-pivot-groovy
    griffon-pivot-java
    griffon-plugin
    griffon-swing-groovy
    griffon-swing-java

Available templates in pledbrook/lazybones-templates

    aem-multimodule-project
    afterburnerfx
    afterburnergfx
    angular-grails
    asciidoctor-gradle
    asciidoctor-revealjs
    dropwizard
    gaelyk
    gradle-plugin
    gradle-quickstart
    groovy-app
    groovy-lib
    java-basic
    lazybones-project
    nebula-plugin
    ratpack
    ratpack-lite
    spring-boot-actuator
    test-handlebars
```
