# linux-led-patch
My patch for the BlinkM LED driver in the Linux kernel

Add multicolor support to the BlinkM driver, making it easier to control
from userspace. The BlinkM LED is a programmable RGB LED. The driver
currently supports only the regular LED sysfs class, resulting in the
creation of three distinct classes, one for red, green, and blue. The
user then has to input three values into the three seperate brightness
files within those classes. The multicolor LED framework makes the
device easier to control with the multi_intensity file: the user can
input three values at once to form a color, while still controlling the
lightness with the brightness file.

The main struct blinkm_led has changed slightly. The struct led_classdev
for the regular sysfs classes remain. The blinkm_probe function checks
CONFIG_LEDS_BLINKM_MULTICOLOR to decide whether to load the seperate
sysfs classes or the single multicolor one, but never both. The
blinkm_set_mc_brightness() function had to be added to calculate the
three color components and then set the fields of the blinkm_data
structure accordingly.

Signed-off-by: Joseph Strauss <jstrauss@mailbox.org>
---
Changes in v2:
- Replaced instances of the constant 3 with NUM_LEDS, where applicable
- Fixed formatting errors
- Replaced loop inside of blinkm_set_mc_brightness() with equivalent
  statements
- Changed id of multicolor class from 4 to 3
- Replaced call to devm_kmalloc_array() with devm_kcalloc()
Changes in v3:
- Add CONFIG_LEDS_BLINKM_MULTICOLOR to check whether to use multicolor
  funcitonality
- Extend well-known-leds.txt to include standard names for RGB and indicator
  LEDS
- Change name of Blinkm sysfs class according to well-known-leds.txt
- Simplify struct blinkm_led and struct blinkm_data
- Remove magic numbers
- Fix formatting errors
- Remove unrelated changes
Changes in v4:
- Fix indentation
- Add default case to switch statement
Changes in v5:
- Fix warnings related to snprintf on s390 architecture, reported by
  0-DAY CI Kernel Test Service
Changes in v6:
- Refactored struct blinkm_led to use a union
- Refactored blinkm_probe()
- Clarified documentation
Changes in v7:
- Fix formatting and spelling errors
Changes in v8:
- Make compatible with Linux -next
