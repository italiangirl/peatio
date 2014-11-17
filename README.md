#### Git Flow

**Overview**

与一般项目不同, peatio_beijing的Git Flow同时有两个目标:

1. 支持peatio\_beijing自身(peatio/peatio_beijing, 也就是Yunbi)的开发工作.
2. 支持peatio开源项目(peatio/peatio)，不断将好的功能输入开源项目。出于这目的，所有需要开源的功能，应该先在peatio开源上实现，再merge到peatio_beijing.

以下peatio_beijing简称yunbi, peatio开源简称peatio。

项目clone到本地之后，需要有origin和peatio_beijing两个remote, 方便开发:

    git clone git@github.com:peatio/peatio.git
    cd peatio
    git remote add peatio_beijing git@github.com:peatio/peatio_beijing.git
    git fetch peatio_beijing
    git checkout -b peatio_beijing --trac peatio_beijing/master


**Branches**

为了支持yunbi的开发，我们需要:

1. production分支. 由Daniel和Jan维护，用于版本发布，是部署production服务器时使用的分支。productino分支只在部署的时候更新，永远与production服务上的代码保持一致。任何时候如果你往production分支push了什么，基本是弄错了。如果production网站出现bug, 需要紧急补丁, 可以在production上直接提交并部署。Daniel/Jan负责把hotfix merge回master分支。
2. master分支. master分支是主要开发分支, 这里集中了下一次大更新要发布的features，以及从production分支merge进来的hotfix.
3. feature分支. feature分支是一个总称，不是具体的分支名字。在实现新feature的时候, 我们需要先基于production开一个新分支，在这个新分支上工作。如果要实现的新feature依赖于另外一个正在开发的feature, 请相关开发人员自行协商在哪个分支工作。feature分支上的工作结束之后，有三种情况:
    a. 这个feature需要立即发布。此时可以联系Daniel/Jan, 将feature分支merge到production分支然后发布。
    b. 这个feature要在下一次大更新时发布。此时将feature分支merge到master分支即可。
    c. 这个feature不知道什么时候发布或者不发布。啥也不干即可。

peatio的开发实际上也需要同样的三种分支，唯一的区别是peatio只是框架，没有production服务器，因此没有production分支，只有stable分支。其他两类分支相同。

peatio和yunbi都需要的feature, 我们总是先在peatio里面实现，再merge到yunbi中。yunbi中的分支永远不会向peatio merge.

**Flow**

你要做的事情是?

1. 实现新feature. 这个feature是只有yunbi需要还是peatio/yunbi都需要?

  a. 只有yunbi需要 -> 从peatio_beijing/production开分支，干活

  b. 都需要 -> 从peatio/stable开分支，干活

2. 修复production上的bug (hotfix). 你能部署服务器吗?

  a. 不能 -> 从peatio_beijing/production开分支，干活, 干完了联系Daniel/Jan

  b. 能. -> 从peatio_beijing/production开分支，或者直接在production提交，干完了部署


#### Major Release Planning/Develop/Test/Deploy Flow

1. Release Planning

  a. 为下一个release增加一个todo: https://tower.im/projects/fd2686cb7fac4b149a537794c67597ac/lists/771fd4e2ff2a485187bb7cafe0732769/show/

  b. todo里面记录这次release要发布的features

2. Release Development

  a. 参考Git Flow创建分支并开发

  b. 提交PR, 将PR的milestone为所属的release, e.g. r2

    b1. 除非有明确说明, PR请求往master merge.

    b2. 特殊情况下deployer可以创建一个<release_name>_master分支，如r2_master, 同时告知开发者该release下的PR往<release_name>_master merge.

  c. Review PR and merge.

  d. 如有任何需要在deploy时完成的任务，如migration task, 配置修改, 记录到tower上的release todo中

3. Release Test

  a. merge release分支到staging

  b. 部署staging

  c. QA (TODO: Define QA Process)

4. Prepare Deploy

  a. 阅读release todo

  b. 部署前服务器准备 (修改配置 etc.)

  c. merge release分支(master或指定release分支)进production. 注意: 尽量避免merge staging分支到production, 因为staging分支可能包含不属于本次release的代码。

5. 部署

  a. 部署

  b. 部署后服务器维护 (运行task etc.)

  c. 部署中遇到的未解决问题记录到tower上

  d. slack中发布部署完成消息, tower上将release todo标记完成

