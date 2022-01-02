---
layout: default
title: "Magic Smoke and PTC's"
date: 2021-12-31
categories: upduino power PTC
comments: true
---

<h2 class="post_title">
  {{ page.title }}
  {% if page.date %}
    <small>, {{ page.date | date_to_string }}</small>
  {% endif %}
</h2>

## Background

@stevenbell teaches a course in Introductory Digital Engineering at Tufts University and has bought many [UPduino's][the_upduino]. He suggested that the ferrite bead be replaced with a [PTC][the_ptc] as he was seeing students shorting out their boards leading to burning out the ferrite bead.

If true, this is a pretty worrisome failure as the board is used by makers as well as students for introductory engineering courses, many of whom are novices and would likely end up with a similar failure.

The ferrite bead is circled in red below.
![UPduino Ferrite bead schematic](/assets/images/magic-smoke/upduino_ferrite_bead_schematic.png)

This was a bit surprising to me as I had always assumed that the USB power was limited to 500mA per the spec and it was the job of the PC or USB power source to handle overcurrent and trip accordingly. 

## Verification of report

The baseline design loaded into the UPduino is the tinyFPGA bootloader with a breathing blue LED. This can be clearly seen in the current consumption.
![UPduino with breathing LED](/assets/images/magic-smoke/upduino_power_up_with_breathing_led.png)

Surprise! When the 5V is shorted to ground, whe short video here shows the ferrite bead glowing like an LED and eventually burning out. This confirms Steven's experience.
![UPduino short without PTC](/assets/images/magic-smoke/upduino_short_without_ptc.gif)

The corresponding current/power trace below shows the bead burning out over about 6 seconds.
![UPduino short burnout](/assets/images/magic-smoke/joulescope_upduino_short_burnout.png)

## USB port currents

So isnt the job of a PC to protect its own USB ports? When does a port trip? Lets try shorting a superspeed USB from my desktop machine:
![Shorting a superspeed USB](/assets/images/magic-smoke/shorting_superspeed_usb.png) 
Wow! The current exceeded the limit of my measuring instrument (10A) and lasted almost 200ms, quite a bit of energy into the short circuit.

Hmm, I wonder if the powered USB hub does any better. Lets try shorting its output:
![Shorting a powered USB hub (Acroname 4 port)](/assets/images/magic-smoke/shorting_USB_output_from_USB_hub.png)
Significantly less current and also very fast to trip.

So it appears that USB ports arent all designed the same and we cannot rely on the USB port to limit the energy into a short circuit.

## Bench top power supply currents
OK, so USB ports are not reliable for trip points. UPduino's are also connected to lab power suplies. Lets see what happens to a lab power supply under a short.

Heres the power supply powering up into a short circuit. The suply is set to 0.5A current limit.
![Power supply turning on into a short](/assets/images/magic-smoke/power_supply_turn_on_into_short.png)
The supply is quite well behaved and does limit curent after a brief turn on spike within a few milliseconds.

How about shorting the output? What happens to the energy in the output capacitors of the power supply?
![Shorting a 5V 0.5A power supply](/assets/images/magic-smoke/shorting_ps_output.png)
Very similar behavior as above so it appears that a lab power supply will not cause damage to the ferrite bead that we're seeing when the UPduino is plugged into a USB port.

# Adding a [PTC][the_ptc]

Since we've proven that significant irriversible damage occurs to the ferrite bead under short circuit of the 5V when powered by a PC USB hub, we would like to fix this so its not a permanent failure. A PTC comes to mind.

PTC's are positive temperature coefficient devices, also known as self-resetting fuses. When a large current passes through the PTC, it heats up and undergoes a material phase change at a specific temperature. This new phase has a significantly higher resistance as compared to the low temperature material phase. The current passing through the PTC in this phase keeps the PTC at a high enough temperature to maintain the higher resistance phase by positive feedback.

Once current is interrupted, this material cools down and changes to the lower resistance phase which is normal operation and so is self resetting assuming the current is interrupted externally.

Heres what happens with the UPduino with the ferrite bead replaced by [the PTC][the_ptc].
![Shorting with PTC on UPduino](/assets/images/magic-smoke/shorting_with_ptc.png)

Heres the zoomed in version where we see that the PTC trips within 2 ms, limiting energy going into the short circuit.
![PTC during short circuit](/assets/images/magic-smoke/joulescope_ptc_zoom_in.png)

Now for final proof that the short circuit is recoverable...
![UPduino short with PTC](/assets/images/magic-smoke/upduino_short_with_ptc.gif)


### How much current does the PTC take to trigger?
Its important to know when the PTC will trigger so that normal operation of the circuit isnt compromised. By setting the power supply soucing the USB into a constant current mode and slowly stepping up the current, we can gt an idea of when the PTC triggers.

As seen below, the PTC triggers when the current through it exceeds 700mA per the spec of the PTC. Of course, this is subject to temperature variation since the PTC is strongly dependent on ambient temperatures. However, there is sufficient margin from the 500mA that the board is supposed to draw.

![Incrementing current into the PTC](/assets/images/magic-smoke/incrementing_current.png)

## Conclusion
A PTC is a great addition to a USB design which might be subjected to short circuits and reduces the chances of damage to circuitry.
 
### Instruments used:
* Joulescope for power measurements
* Acroname 4 port powered USB hub
* Siglent SPD3303X-E power supply
* Custom built PC with Windows 10
* [PTC][the_ptc]
* [UPduino 3.0][the_upduino]

[the_ptc]: https://www.digikey.com/en/products/detail/bourns-inc/MF-FSMF035X-2/2039255
[the_upduino]: https://www.tindie.com/products/tinyvision_ai/upduino-v30-low-cost-lattice-ice40-fpga-board/

{% include disqus.html %}