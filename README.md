[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![GPL3 License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]
[![Ask Me Anything][ask-me-anything]][personal-page]


<!-- PROJECT LOGO -->
<br />
<p align="center">
  <a href="https://github.com/stiliajohny/Vagrant-Dev-Box">
    <img src="https://github.com/stiliajohny/Vagrant-Dev-Box/raw/main/.assets/logo.png" alt="Logo" width="80" height="80">
  </a>

  <h3 align="center">Vagrant Dev Box</h3>

  <p align="center">
    Quick and easy Development Box build with Ansible and Vagrant
    <br />
    <a href="./README.md"><strong>Explore the docs »</strong></a>
    <br />
    <a href="https://github.com/stiliajohny/Vagrant-Dev-Box/issues/new?labels=i%3A+bug&template=1-bug-report.md">Report Bug</a>
    ·
    <a href="https://github.com/stiliajohny/Vagrant-Dev-Box/issues/new?labels=i%3A+enhancement&template=2-feature-request.md">Request Feature</a>
  </p>
</p>

<!-- TABLE OF CONTENTS -->

## Table of Contents

- [Table of Contents](#table-of-contents)
- [About The Project](#about-the-project)
  - [Built With](#built-with)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
  - [Web accesible apps](#web-accesible-apps)
  - [i3-wm keybinds](#i3-wm-keybinds)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)
- [Acknowledgements](#acknowledgements)

<!-- ABOUT THE PROJECT -->

## About The Project

<p align="center">
  <a href="https://github.com/stiliajohny/Vagrant-Dev-Box">
    <img src="https://github.com/stiliajohny/vagrant-dev-box/raw/main/.assets/screenshot0.png" alt="Logo" height="400"><img src="https://github.com/stiliajohny/vagrant-dev-box/raw/main/.assets/screenshot1.png" alt="Logo" height="400">
  </a>
</p>

I always wanted to have a quick way to build a VM that contains all of my favorite tools and configs.
Here is my take on a Dev Box build with Ansible and Vagrant.


Use it carefully.
### Built With

- Vagrant
- Ruby
- Ansible


---


## Getting Started



### Prerequisites

- Ansible
- Vagrant
- VirtualBox ( My preferred way for Virtualization )

### Installation

- Ansible [Official Installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- Vagrant [Official Installation guide](https://www.vagrantup.com/docs/installation)
- VirtualBox ( My preferred way for Virtualization )  [Official Installation guide](https://www.virtualbox.org/wiki/Downloads)

---

## Usage

- Git Clone the repo
- cd in the cloned repo
- Run
   -  `vagrant up` to start and provision the instance
   -  `vagrant provision` to re-provision the instance
   -  `vagrant pause` to pause the instance
   -  `vagrant resume` to resume a previously paused instance
   -  `vagrant destroy` to completely destroy the instance
   -  `vagrant ssh` to ssh in the instance
   -  `vagrant global-status` to get all the running instances ID
   -  `vagrant ssh 123abc` to ssh in the specific instance via the ID gathered earlier

### Web accesible apps
- VNC on browser ( Xorg Display ) - http://localhost:6081/vnc.html)
- VNC on browser ( Virt Display ) - http://localhost:6081/vnc.html
- VSCode on browser - http://localhost:8080

### i3-wm keybinds
- <kbd>Ctrl</kbd> - Mod key
- <kbd>Ctrl</kbd>+<kbd>d</kbd> - rofi menu

---

<!-- ROADMAP -->

## Roadmap

See the [open issues](https://github.com/stiliajohny/Vagrant-Dev-Box/issues) for a list of proposed features (and known issues).

---

<!-- CONTRIBUTING -->

## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

<!-- LICENSE -->

## License

Distributed under the GPL-3.0 License. See `LICENSE` for more information.

<!-- CONTACT -->

## Contact

Your Name - [@john_stilia](https://twitter.com/john_stilia) - stilia.johny@gmail.com

<!--
Project Link: [https://github.com/your_username/repo_name](https://github.com/your_username/repo_name)
-->

---

<!-- ACKNOWLEDGEMENTS -->

## Acknowledgements

- [GitHub Emoji Cheat Sheet](https://www.webpagefx.com/tools/emoji-cheat-sheet)
- [Img Shields](https://shields.io)
- [Choose an Open Source License](https://choosealicense.com)
- [GitHub Pages](https://pages.github.com)

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->

[contributors-shield]: https://img.shields.io/github/contributors/stiliajohny/Vagrant-Dev-Box.svg?style=for-the-badge
[contributors-url]: https://github.com/stiliajohny/Vagrant-Dev-Box/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/stiliajohny/Vagrant-Dev-Box.svg?style=for-the-badge
[forks-url]: https://github.com/stiliajohny/Vagrant-Dev-Box/network/members
[stars-shield]: https://img.shields.io/github/stars/stiliajohny/Vagrant-Dev-Box.svg?style=for-the-badge
[stars-url]: https://github.com/stiliajohny/Vagrant-Dev-Box/stargazers
[issues-shield]: https://img.shields.io/github/issues/stiliajohny/Vagrant-Dev-Box.svg?style=for-the-badge
[issues-url]: https://github.com/stiliajohny/Vagrant-Dev-Box/issues
[license-shield]: https://img.shields.io/github/license/stiliajohny/Vagrant-Dev-Box?style=for-the-badge
[license-url]: https://github.com/stiliajohny/Vagrant-Dev-Box/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/johnstilia/
[product-screenshot]: .assets/screenshot.png
[ask-me-anything]: https://img.shields.io/badge/Ask%20me-anything-1abc9c.svg?style=for-the-badge
[personal-page]: https://github.com/stiliajohny

https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
https://img.shields.io/github/license/stiliajohny/Vagrant-Dev-Box.svg?style=for-the-badge
