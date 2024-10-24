---
tip: translate by baidu@2024-10-23 15:59:26
---

# Autoware - the world's leading open-source software project for autonomous driving

![Autoware_RViz](https://user-images.githubusercontent.com/63835446/158918717-58d6deaf-93fb-47f9-891d-e242b02cba7b.png)

Autoware is an open-source software stack for self-driving vehicles, built on the [Robot Operating System (ROS)](https://www.ros.org/). It includes all of the necessary functions to drive an autonomous vehicles from localization and object detection to route planning and control, and was created with the aim of enabling as many individuals and organizations as possible to contribute to open innovations in autonomous driving technology.

> Autoware 是一个基于[机器人操作系统(ROS)]的自动驾驶汽车开源软件栈(https://www.ros.org/). 它包括驾驶自动驾驶汽车所需的所有功能，从定位和物体检测到路线规划和控制，其目的是让尽可能多的个人和组织为自动驾驶技术的开放式创新做出贡献。

![Autoware architecture](https://static.wixstatic.com/media/984e93_552e338be28543c7949717053cc3f11f~mv2.png/v1/crop/x_0,y_1,w_1500,h_879/fill/w_863,h_506,al_c,usm_0.66_1.00_0.01,enc_auto/Autoware-GFX_edited.png)

## Documentation

To learn more about using or developing Autoware, refer to the [Autoware documentation site](https://autowarefoundation.github.io/autoware-documentation/main/). You can find the source for the documentation in [autowarefoundation/autoware-documentation](https://github.com/autowarefoundation/autoware-documentation).

> 要了解有关使用或开发 Autoware 的更多信息，请参阅[Autoware 文档网站](https://autowarefoundation.github.io/autoware-documentation/main/). 您可以在[autowarefoundation/autoware documentation]中找到文档的来源(https://github.com/autowarefoundation/autoware-documentation).

## Repository overview

- [autowarefoundation/autoware](https://github.com/autowarefoundation/autoware)

  - Meta-repository containing `.repos` files to construct an Autoware workspace.

  - It is anticipated that this repository will be frequently forked by users, and so it contains minimal information to avoid unnecessary differences.

> -预计该存储库将经常被用户分叉，因此它包含的信息最少，以避免不必要的差异。

- [autowarefoundation/autoware_common](https://github.com/autowarefoundation/autoware_common)
  - Library/utility type repository containing commonly referenced ROS packages.
  - These packages were moved to a separate repository in order to reduce CI execution time
- [autowarefoundation/autoware.core](https://github.com/autowarefoundation/autoware.core)

  - Main repository for high-quality, stable ROS packages for Autonomous Driving.

  - Based on [Autoware.Auto](https://gitlab.com/autowarefoundation/autoware.auto/AutowareAuto) and [Autoware.Universe](https://github.com/autowarefoundation/autoware.universe).

> -基于[Autoware.Auto](https://gitlab.com/autowarefoundation/autoware.auto/AutowareAuto)以及[Autoware.Univers](https://github.com/autowarefoundation/autoware.universe).

- [autowarefoundation/autoware.universe](https://github.com/autowarefoundation/autoware.universe)

  - Repository for experimental, cutting-edge ROS packages for Autonomous Driving.

  - Autoware Universe was created to make it easier for researchers and developers to extend the functionality of Autoware Core

> -Autoware Universe 的创建是为了让研究人员和开发人员更容易扩展 Autoware Core 的功能

- [autowarefoundation/autoware_launch](https://github.com/autowarefoundation/autoware_launch)
  - Launch configuration repository containing node configurations and their parameters.
- [autowarefoundation/autoware-github-actions](https://github.com/autowarefoundation/autoware-github-actions)

  - Contains [reusable GitHub Actions workflows](https://docs.github.com/ja/actions/learn-github-actions/reusing-workflows) used by multiple repositories for CI.

> -包含[可重用的 GitHub 操作工作流](https://docs.github.com/ja/actions/learn-github-actions/reusing-workflows)由多个 CI 存储库使用。

- Utilizes the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) concept.
- [autowarefoundation/autoware-documentation](https://github.com/autowarefoundation/autoware-documentation)

  - Documentation repository for Autoware users and developers.

  - Since Autoware Core/Universe has multiple repositories, a central documentation repository is important to make information accessible from a single place.

> -由于 Autoware Core/Universe 有多个存储库，因此一个中央文档存储库对于从一个地方访问信息非常重要。

## Using Autoware.AI

If you wish to use Autoware.AI, the previous version of Autoware based on ROS 1, switch to [autoware-ai](https://github.com/autowarefoundation/autoware_ai) repository. However, be aware that Autoware.AI has reached the end-of-life as of 2022, and we strongly recommend transitioning to Autoware Core/Universe for future use.

> 如果你想使用 Autoware。AI，基于 ROS 1 的 Autoware 的前一个版本，切换到[Autoware AI](https://github.com/autowarefoundation/autoware_ai)存储库。但是，请注意 Autoware。截至 2022 年，人工智能已经达到了生命周期的终点，我们强烈建议过渡到 Autoware Core/Universe 以供将来使用。

## Contributing

- [There is no formal process to become a contributor](https://github.com/autowarefoundation/autoware-projects/wiki#contributors) - you can comment on any [existing issues](https://github.com/autowarefoundation/autoware.universe/issues) or make a pull request on any Autoware repository!

  - Make sure to follow the [Contribution Guidelines](https://autowarefoundation.github.io/autoware-documentation/main/contributing/).

> -确保遵循[贡献指南](https://autowarefoundation.github.io/autoware-documentation/main/contributing/).

- Take a look at Autoware's [various working groups](https://github.com/autowarefoundation/autoware-projects/wiki#working-group-list) to gain an understanding of any work in progress and to see how projects are managed.

> -看看 Autoware 的[各种工作组](https://github.com/autowarefoundation/autoware-projects/wiki#working-小组列表)，以了解任何正在进行的工作，并了解项目是如何管理的。

- If you have any technical questions, you can start a discussion in the [Q&A category](https://github.com/autowarefoundation/autoware/discussions/categories/q-a) to request help and confirm if a potential issue is a bug or not.

> -如果您有任何技术问题，可以在[Q&a]类别中开始讨论(https://github.com/autowarefoundation/autoware/discussions/categories/q-a)请求帮助并确认潜在问题是否是错误。

## Useful resources

- [Autoware Foundation homepage](https://www.autoware.org/)
- [Support guidelines](https://autowarefoundation.github.io/autoware-documentation/main/support/support-guidelines/)
- [CI metrics](https://autowarefoundation.github.io/autoware-ci-metrics/)
