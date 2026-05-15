---
tags:
  - asdf
  - explanation
references:
  - https://asdf-vm.com/guide/introduction.html#how-it-works
  - https://asdf-vm.com/manage/versions.html#shims
aliases:
  - how-asdf-work
---
# asdf가 tool version을 실행하는 방식

## plugin

> [!Quote]
> 
> Once `asdf` core is set up with your Shell configuration, plugins are installed to manage particular tools. When a tool is installed by a plugin, the executables that are installed have [shims](https://en.wikipedia.org/wiki/Shim_\(computing\)) created for each of them. When you try and run one of these executables, the shim is run instead, allowing `asdf` to identify which version of the tool is set in `.tool-versions` and execute that version.

asdf는 plugin 시스템 기반

plugin은 특정 tool을 관리함
- 특정 tool version을 설치하고 관리

## package와 executable

asdf가 plugin을 통해 어떤 tool version을 설치하면, 그 설치 결과는 package

package 안에는 사용자가 실행할 수 있는 executable program들이 들어 있음

## shim

> [!Quote]
> 
> When asdf installs a package it creates shims for every executable program in that package in a `$ASDF_DATA_DIR/shims` directory (default `~/.asdf/shims`). This directory being on the `$PATH` (by means of `asdf.sh`, `asdf.fish`, etc) is how the installed programs are made available in the environment.

shim은 asdf가 설치한 package 안의 executable program마다 만들어지는 작은 wrapper

> [!quote]
> 
> The shims themselves are really simple wrappers that `exec` a helper program `asdf exec` passing it the name of the plugin and path to the executable in the installed package that the shim is wrapping.

shim은 다음과 같이 동작
- [`asdf exec`](https://asdf-vm.com/manage/core.html)라는 helper program을 실행후 다음을 전달
	- plugin 이름
	- installed package 안의 shim이 감싸고 있는 executable path
		- 여러 버젼이 존재할 수 있어서 최종 실행 path는 아님

## asdf exec

> [!quote]
> 
> The `asdf exec` helper determines the version of the package to use (as specified in `.tool-versions` file or environment variable), the final path to the executable in the package installation directory (this can be manipulated by the `exec-path` callback in the plugin) and the environment to execute in (also provided by the plugin - `exec-env` script), and finally it executes it.

`asdf exec`는 다음 과정을 거침
- 사용할 package version 결정
	- `.tool-versions` 파일이나 환경 변수 기준
- 최종 executable path 결정
- plugin 에서 커스터마이징한 동작 실행
	- plugin - `exec-env` script
	- `exec-path` callback in the plugin
- 최종 실행
