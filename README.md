G-Code Navigator
----------------

**[<span style="color:#30A0D8;">G-Code</span>][RepRap wiki G-code page] language support for
IntelliJ platform**

**Version 0: currently a placeholder for a future project.**

A plugin whose aim is to allow experimentation and optimization of 3D FDM printer for higher
speeds, quality or both by working with G-Code files produced by a slicer.

Goals:

1. Preview of the 3D model generated by the G-Code
2. Allow modification of G-Code to allow experimentation with new approaches to improve print
   quality
3. Analyze G-Code for various parameters such as acceleration, filament feed rate, etc
4. Semi-Automated 3D printer calibration procedures to eliminate guesswork of printing
   parameters
5. Preview of "actual" printed result based on calibrated values, including actual: extrusion
   width, layer sagging (bridged), layer height.
6. Generate optimized G-Code based on a calibrated printer model and improved printing methods.

## Development Approach

The goal is to have a complete G-Code experimentation workbench which is OS independent with
project based multi-file environment.

JetBrains OpenAPI was chosen because:

1. It offers a project oriented platform with rich version control, editing features, multi-file
   java environment with great multi-OS implementation.
2. Is available in a free, open source version
3. I am familiar with it and don't want to spend time on java OS specific idiosyncrasies.

Implementation will proceed by creating a usable plugin as fast as possible, followed by
multiple iterations for improvements as time permits and understanding on how to do it is
reached.

## Motivation 

I got a TEVO Little Monster 3D printer (a delta printer) and got hooked on 3D printing. At the
same time I have a very high degree of frustration with the amount of "tweaking" and manual
intervention required to the slicer settings for each individual model. The amount of re-prints
due to "operator" forgetfulness or error is also high since the slicer offers a ton of settings
without much "intelligence" of varying these settings automatically.

Effectively, each 3D model needs to be tweaked for its optimum print parameters for each type of
filament that is used.

All this is greatly exaggerated if you are not happy to print at 40 or 60 mm/s and want to get
good quality prints at 100 mm/s or higher. At this speed the slicer will not be of much help
and neither will be the printer firmware because the real behavior of the printer and "modeled"
parameters are completely out of sync.

Additionally, the effect of slicer settings is not intuitive. Many are inter-dependent and
cannot be optimized because they are affected by other settings which vary during printing.

Spending hours observing the printer printing while trying to understand how the printer really
behaves led me to the conclusion that most if not all slicer and firmware tweaks are based on
parameters that poorly model behavior of the actual printing process. Hence their lack of
intuitiveness and difficulty of optimization except for a very narrow range of printer
capabilities.

Sometimes, at higher speeds or greater layer heights, the results are diametrically opposed to
what you would expect because now the behavior of the print head and extruded plastic is no
longer approximated by absolute nozzle position and a laser beam like filament deposition but is
better viewed as an elastic rope falling down from a pendulum. Final resting place of the
filament depends on extrusion delay, time taken by filament to reach from the nozzle to previous
layer and actual nozzle movement. A small change in filament "actual" deposition time (like
changing the retraction length or speed) can disproportionately affect where the filament is
deposited and lead to a surprising change in quality without obvious reasons.

Since the effect of changes to settings is not intuitive, it takes a long time to develop a
decent model of the printer's behaviour through trial and error before you can intelligently
change settings to improve results.

Most of the time the trial and error effort takes hours. To reduce the calibration time, people
have created 3D models for calibration which can be used to test out the printer settings. All
these models suffer from several shortcomings:

1. They are either too simple and print fast without exercising enough of the printer's range
   that will be used in a real model or they are complex enough to take significant amount of
   time to print.

2. The models themselves have too many setting parameters affecting the result and do not help
   to isolate the effects of each setting. This makes it very difficult to figure out which of
   the myriad of slicer settings is the main cause contributing to the poor quality of the
   print. This leads to many iterations of trial and error, taking hours while leading to less
   than optimum improvements.

3. The calibration models cannot take all possible circumstances of a real model into account
   and the settings must be calibrated to each specific print model if quality and print speed
   is to be maximized.

The only solution that is currently used to address dynamic mechanical errors in the 3D printer
mechanism is to reduce print (extruder/hot-end) speed or similarly its acceleration.

### Assisted Calibration

The calibration should be implemented by producing a set of prints and asking to pick the "best"
result on some **visual** criteria. Based on this choice the software should be able to determine a
range for some performance parameter and proceed to produce the next set that would allow it to
determine an improved value for this parameter.

The calibration procedure should calibrate the parameters in the right sequence with the most
critical and on which others depend, first.

In cooking you should always adjust the quantity of salt to taste then other flavours, including
sugar, because the salt has a disproportional effect on the perception of other flavours.

Similarly, the 3D printer needs to have some basic parameters calibrated before proceeding to
the calibration of more minor parameters.

Each calibration procedure should be as short as possible, isolate individual parameters of the
dynamic printer model as much as possible and allow use of relative **visual** inspection of the
results to select the models with the best parameter value.

Each set of calibration procedures should isolate as much as possible each parameter of the
printer so that its optimum value could be **visually** determined from a simple printed
results. An important part of each procedure is to reduce the print time to seconds rather than
minutes so that this procedure can be iterated quickly to an optimal parameter value.

When a single parameter cannot be accurately represented by a single value then the calibration
procedure should be performed under various conditions and optimized for a range of another
parameter on which it depends. Then the value of this parameter during printing can be
interpolated based on current printing conditions without needing user intervention.

The assisted calibration procedure would go a long way to help optimize the printer results for
someone with less experience by presenting the user with pictures to help them choose what
results they are actually getting.

The goal is to allow calibration for a completely new filament to be accomplished in about an
hour with guaranteed optimized results, usable across the full range of printer capabilities and
a known valid range of print parameters. The latter would allow:

1. To detect when a particular print model is outside a calibrated range and informing the user
   of that fact.
2. Allow for a smaller calibration range and only calibrating over a greater range as
   necessitated by a subsequent model about to be printed.

From my experience this would be a factor of 8 or 10 improvement over the time it takes now to
calibrate a filament for printing. Give certainty as to optimization level of the result and
ability to use these settings on dissimilar 3D models.

### Allow Experimentation

Experimentation with new approaches to solving 3D printing approaches requires creating
customized G-Code files or editing ones created by the slicer.

I have a few ideas which may lead to significant improvements but I cannot try out because I
would have to either do all the coordinate computations by hand or write a dedicated program to
generate the G-Code segment in question.

* Dynamic print head movement and extruder models to improve quality while maximizing speed
* Bridging approaches which will improve the quality of unsupported areas of the print model
* Improved support structure generation to reduce amount of support material and reduce printing
  time.
* Filament temperature model to better control when it is possible to deposit the next layer.

The idea for the plugin is to allow analysis and intelligent modification of the G-Code so that
intelligent modification of the G-Code can be done while preserving the 3D model that the
original G-Code models.

At first the ideas can be tried out by selecting an area of a layer in the preview and having
the plugin modify the G-Code to have the loop stay within an area. Then the given segment of the
loop can have its print parameters modified: speed, extrusion amount, fan speed, etc.

Once it is experimentally verified that the idea has merit, then code can be written to allow
such "optimization" to be done semi-automatically and then automatically.

Some discussion of these ideas follows.

### Improved Movement and Extrusion Models

A better model of the actual movement of the print head and extruded filament would go a long
way to allow easier calibration with improved quality of resulting prints at same or higher
speeds.

The print head, especially on a delta printer, should be modeled as a spring supported mass.
When the print direction changes the actual print head will oscillate before settling into the
new movement direction. This is easily seen on 3D print corners. The print direction of the
corner can be determined by looking for the overshoot of the corner and subsequent oscillation
around the new direction. On my printer this is very visible at 80 mm/s and higher.

The movement of the nozzle during deceleration and acceleration is not linear. The real nozzle
will lead during deceleration and lag during acceleration compared to the "virtual" nozzle.
Since the firmware adjusts the extrusion speed linearly this causes non-uniform deposition of
filament where nozzle direction changes.

There is a time delay between change in extruder feed rate and feed rate of the extruded
filament. The simplest improvement would be to model this delay and adjust the extruder feed
rate to result in an improved extrusion of the real filament.

Trying to address this with only limiting acceleration does not help since setting the
acceleration low enough to eliminate this overshoot and vibration effectively reduces the
printer speed to about 40 mm/s, making print times unnecessarily long.

One improvement is to use non-constant acceleration and deceleration to eliminate overshoot and
accompanying ringing.

If you drive a car then you probably do this automatically when breaking. You don't keep the
deceleration constant because that would cause excessive breaking distance. Instead you
decelerate faster at first and as you approach the location where you want to stop you ease up
on the break pedal so that you come to a soft stop. If you reduce deceleration too quickly, you
will overshoot your desired location. If you don't reduce the deceleration to zero as you come
to a full stop, you and the passengers will experience backlash as the car stops. The print head
behavior is the same. If a perfectly compensated acceleration curve is used it would allow the
nozzle to behave as a critically damped oscillator, maximizing acceleration and eliminating
overshoots and oscillations about the direction of motion.

Some people have implemented S-curve acceleration in firmware to improve the smoothness of
motion at high speeds or proper jerk control (third derivative of distance). This should be done
in the printer firmware but for ease of experimentation this could be done through G-Code
control and once validated, implemented in the printer firmware.

Consistency of extruded plastic has a drastic effect on quality of printed results. Currently,
extrusion control uses linear velocity model and adjusts the extrusion speed accordingly. The
real movement of the nozzle is does not follow this linear velocity model and causes irregular
filament deposition unless acceleration is reduced to levels where the linear model is
sufficiently accurate.

The extruder feed rate should be adjusted to reflect the real nozzle velocity and extrusion
behavior to result in filament deposition that approaches "ideal model". The improvement in
print quality would be significant.

Special consideration should be given to modeling retraction and un-retraction. Extruder
behavior will vary greatly between direct and boden configurations and also between boden
configurations depending on the length of the tube and filament type.

Retraction calibration should be able to determine if there is a delay between stopping
extrusion of plastic from the nozzle and the time retraction of the filament is started in the
extruder. This delay should be used to start the retraction operation in advance of where
extruded filament retraction is needed. Slicers have a "coast distance" setting which does not
address this delay and without assisted calibration is impossible to determine.

Calibration procedures for retraction should allow to **visually** select the transition form
insufficient retraction to optimum to excess retraction. This calibration should be performed
for a range of extrusion speeds to ensure that if it is affected by extrusion speed then
optimization will take the speed into account.

Once retraction has been optimized, un-retraction should similarly be calibrated to ensure the
nozzle is returned to the "just about to extrude" condition.

Although the effort to create accurate movement, extrusion models and calibration procedures may
be significant, it will allow to print at speeds of 100, 120 or even 150 mm/s while maintaining
print quality.

I feel the effort is worth the pain if it results in getting a quality print in under an hour
rather than three or more hours.

### Improved Bridging

For example, to improve bridging results it is not possible to use only print speed and
extrusion amount to create "perfectly flat" bridges.

The loop of the deposited filament should be changed to maximize quality of the resulting bridge
rather than print speed since the amount of bridging is almost always insignificant compared to
the rest of the model but the quality of the bridge will affect the quality of several layers
above it, making bridging quality an important factor for the overall quality.

1. Need to model the sag of the extruded filament and based on that raise the nozzle so that
   once the filament sags it will result in the filament resting at the correct height.

2. The part cooling fans should be increased to stiffen the extruded filament and improve the
   quality of the result. However, fans do not change speed right away and require some delay.
   At the same time increased fan speed should not be done where layer adhesion would be
   affected.

All these variables require more complex control of G-Code than just set fan speed to X% and
speed to Y% and extruder to Z%. Bridging calibration will vary by filament type, filament batch,
extrusion temperature, print speed and probably other factors. Rather than optimizing these on a
per 3D model basis it would be more efficient to create a calibration table that can be used to
select optimum parameters based on current circumstances.

Once bridging quality can be reliably improved, the next step would allow elimination of some
support structures completely or significantly reducing them since they could be replaced with
smaller area supports and high quality bridging a few layers before the full support is needed.
This too would significantly reduce print times for models that require extensive supports.

#### Improved Arc Bridges

One area where I find printing is very flaky is arcs on bridged areas. This happens when you
need an arc outline printed on a support structure with a single layer between the support and
the part to allow removal of the support.

The problem here is that the distance between the nozzle and the support causes the extruded
filament to sag without being attached for a longer period of time, while the nozzle moves on an
arc. The final location of deposited filament will often be a straight line between points on
the arc instead of an arc.

To improve the result, the print speed for this segment should be reduced and the nozzle
location should be offset so that the deposited location of the filament is correct once it
reaches the support underneath.

### Improved Support Structures

Although I do not have a multi-material printer, one complaint everyone points out with soluble
support materials is the high costs of the filament.

One easy cost improvement of 3D multi-material prints is to reduce the use of high cost
materials by using more expensive filament in areas of the model where actual material is
significant such as contact areas between supports and part and in areas where there is no
access to remove the supports.

Using soluble supports only for the few layers where supports come in contact with the part
while the rest of the support structure is printed with the cheaper non-soluble filament, will
make the additional cost of soluble filament insignificant for most models. This would allow the
support material to be more widely used.

### Filament Temperature Model

The basic idea behind this is to better control when it is possible to extrude the next layer
over an area of the part.

Current slicers do not have such a model and try to hack this through print speed reduction,
sacrificial pillars or other indirect means.

This is not sufficient if you are extruding continuously into the same area, even at lower
speeds and causes extrusion over a still soft previous layer. In this case, retraction, backing
off, un-retraction may be necessary to allow for sufficient cooling.

If the G-Code is generated keeping in mind how the extruded filament cools off and when the
layer is solid enough to accept extrusion of the next layer, much of hand tweaking will become
unnecessary since the program will be able to optimize extrusion to allow for sufficient cooling
of previous layers, or inserting pauses to allow for such cooling.

[RepRap wiki G-code page]: http://reprap.org/wiki/G-code

