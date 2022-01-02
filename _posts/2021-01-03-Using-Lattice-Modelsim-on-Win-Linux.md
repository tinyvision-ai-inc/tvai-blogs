---
layout: default
title: "Using Lattice Modelsim on Windows and Linux"
date: 2021-01-03
categories: modelsim windows linux radiant lattice
comments: true
---
<h2 class="post_title">
  {{ page.title }}
  {% if page.date %}
    <small>{{ page.date | date_to_string }}</small>
  {% endif %}
</h2>

I am a supporter of open source and have benefitted greatly from using open tools like verilator and Yosys. However, there are many times when its simply not possible to use these tools with a simulator like verilator since the the FPGA vendors provide IP that are encrypted for either FPGA hard macros such as the Serdes or higher level blocks like MIPI. Component vendors also provide encrypted models of their parts like memories assuming one of the closed simulators.

These encrypted IP cannot be simulated on an open source toolchain. Language support also was lacking for some System Verilog constructs I liked to code with at the time (packed struct).

I tend to use Lattice FPGA's quite a bit it in my work, on things like the [UPduino][the-upduino]. This blog demonstrates how to setup your environment assuming you have already installed the Lattice Semiconductor Radiant toolchain. The Radiant tolchain is relatively small and not bloatware like the Xilinx toolchain and since it works on both Linux and Windows, enables a developer to now have access to a commercial simulation environment with minimal overhead.

## Setup

I prefer to use command line tools such as make for all my work and detest GUI's as they are not automatable and require documentation to run properly and even then, subject to user errors. As such, I like to install `git bash` on my windows machine. Now, its possible to setup WSL2 as well and that should work fine too.

The isntructions are pretty similar for Linux and can be modified slightly where required. 

- Install git bash
- Add make to Git bash by following instructions [here](https://gist.github.com/evanwill/0207876c3243bbb6863e65ec5dc3f058)
- Open a new Git bash shell and type in `make` to check for proper `make` installation
- Install the [Lattice Radiant toolchain](https://www.latticesemi.com/LatticeRadiant)
- Add the following to your path (depends on your Lattice tool installation) in the windows environment variables.

    `C:\lscc\radiant\3.0\modeltech\win32loem`

    `C:\lscc\radiant\3.0\bin\nt64`

- Point the `LM_LICENSE_FILE` variable to the location of the license.dat file which holds your Lattice and other tool licenses by add a System Variable called `LM_LICENSE_FILE` which in my case is: `C:\lscc\radiant\3.0\license\license.dat`
- Type in `vlog` and ensure that the Modelsim Verilog compiler gets called. Please note that this should be the Lattice FPGA version and not something else. This is important to have access to the pre-compiled libaries for the FPGA. If you already know about this, this limitation doesnt apply to you as you know what you're doing. You should get the following output:

`$ vlog`

`Model Technology ModelSim - Lattice FPGA Edition vlog 2020.3 Compiler 2021.02 Feb 11 2021`


[the_upduino]: https://www.tindie.com/products/tinyvision_ai/upduino-v30-low-cost-lattice-ice40-fpga-board/

{% include disqus.html %}
