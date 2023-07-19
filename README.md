
# TopToMidi Documentation

*A general-purpose visual sonification **TouchDesigner** component by **Function Store (Daniel Molnar)** ([@function.str](https://instagram.com/function.str); dan@functionstore.xyz).*

*IMPORTANT NOTICE: The component does not work out-of-the box on **Mac** machines due to the lack of  [td_pip](https://td-olib.org/component/td-pip) support. You can still install the dependency manually to your machine probably.*
** The component is available on my [Patreon](https://www.patreon.com/posts/toptomidi-sonify-85984813), this page serves documentation purposes**

# Glossary

- **Sonification**: The process of converting data or information into sound or audible signals to facilitate analysis or understanding of complex patterns or phenomena.
- **Sampling**: The process of obtaining the color values of specific pixels in an image.
- **VST:** Short for Virtual Studio Technology, is a software interface standard used to integrate audio synthesizer and effect plugins with audio editors and recording systems.
    - You can run VSTs in TouchDesigner or any DAW (Reaper is a good free one)
- **MIDI**: Musical Instrument Digital Interface, is a technology that allows musical instruments and computers to exchange information such as pitch, tempo, and volume, enabling them to create and control music together.
    - MIDI notes have pitch and velocity.
    - Can control **VST**s with it, or hardware synthesizers.
- **Scale Mode:** Variations of a musical scale that start on different notes but follow the same overall pattern, providing different emotional flavors or moods to the music.
- **Root Note:** The first and primary note of a musical scale or chord from which the rest of the notes are derived, often providing the foundational pitch that defines the key of a piece of music.

# Getting Started

- This component allows you to sonify TOPs in TouchDesigner. It is a “general-purpose” component armed with everything you might need for that, but this comes at the cost of complexity, so see the next point.
- **Please read this documentation carefully and see my demonstration video: <LINK>**
- `TopToMidi_DEMO.toe` will get you started with some examples.
- If the component shows an error on startup, just disable and re-enable cooking, or right click and `Clear (Children) Script Errors`
    - Otherwise contact dan@functionstore.xyz

# Input slots

1. The only input slot of the component is the TOP to be sampled.

# Parameters

*The only input slot of the component is the TOP to be sampled.*

## **Input** tab

- **Sampler:** TOP or SOP operator to define the pixel positions for sampling. These can be animated too, of course!
- **Offset 0.5:** By default many SOPs are centered around the origin. This quick toggle shifts the **Sampler**’s center to (0.5, 0.5).
- **Sample Preview:** Opens a UI window to visualize and manually adjust the sampling points. **Move** them around with `Left-Click+Dragging` ; **Scale** with `Scroll-Wheel` (or `+-`), **Reset** with `Middle-Click` .
    - It also has some options on the bottom left to always show the points, toggle **Offset 0.5** and **Reset** the adjustments
    
- **RGB:** Which attribute of the input image to perform further processing on.
    - Can be Luminance, Red, Green, etc., depends on the use-case. Can be left simply on `Luminance` or `RGB Average`**.**
- **Threshold:** Pre-processes the input image by thresholding between two values. This will also have an effect on when a note is triggered in `**Index` Mapping Mode.**
- **Re-Range:** Re-ranges the thresholded image to 0-1. This parameter is only interesting in **`Value`** **Mapping Mode.**

- **Mapping Mode:** This is the most **important** parameter to determine the mapping of pixel values to MIDI notes, *before* assigning to a **Scale.**
    - **`Index`:** The point/pixel indices of the **Sampler** determine the pitch of the notes, upon triggered by the sampled value reaching the **Threshold**. Notes are sustained while in the range. It is important to set a good **Threshold** with **`Index`** mode.
        - Important to note that the number of **Steps** is determined by the number of points. Be careful not to use big textures or SOPs with many points (anyway MIDI is limited to 128 notes).
    - **`Value`**: The sampled values determine the pitch of the notes, where the quantization is determined by the **Steps** parameter.
        - It means if you have identical sampled values they will trigged the same note twice.
        - **Re-Range** of the **Threshold** is suggested to be kept on usually.
        - In this mode you are more free to set more sampling points!
    
- **Trigger Options:** These parameters can give you more control how often/how long notes are being triggered. Can be very useful in busy scenes.
    - **Re-Trigger Gate Length:** This is an overall gate length in seconds. At 0 each trigger will be let through to the next stage, increasing it to e.g. 1 will only generate a new trigger every 1 seconds.
    - **Trigger Gate Length:** Determines how long a trigger needs to be active in order to generate a note.
    - **Min Sustain Length:** How long a note is kept on after triggered.
    - **Re-Trigger Delay:** How often the same generated note can be triggered.

## **Scale** tab

- **Scale:** Determines the Scale Mode the triggered notes are mapped to. Scales determine a number of notes in a Scale, starting from a root note (**Key**). You can also define **Custom Scales** which will also show up in the dropdown!
    - `**MIDI Scale`** is a special one, see later.
- **Key:** Sets the musical root note of the **Scale**.
    - Can type any musical note notation, even wild things like `A#bbb##` (thanks to the [mingus](https://github.com/bspaans/python-mingus) library). But usually the drop-down list should be more reasonable :)
- **Octave:** The octave of the root note.
- **Steps:** Number of MIDI notes to generate.
    - In **`Index`** mode it is fixed to the number of sampling points.
    - In `**Value`** mode it is the "quantization" of the pitch determined by the sampled pixel values.
- **Extend Mode:** When having more steps than the number of notes in the **Scale**, having this on will extend subsequent steps to the next octaves. Otherwise the same scale notes in the starting octave are repeated.
- **Edit Custom Scales:** Opens a Table DAT where in each column you can define a custom scale name where each note can be defined in the rows below using standard musical notation. For example: C#3.
- **Keytrack Lookup:** A multi-sample CHOP reference which can define the MIDI velocity based on the pitch.

- **MIDI Scale:** The parameters below are only relevant if **Scale** is set to `**MIDI Scale**`. They allow you to use this component as a live companion musical instrument.
    - **MIDI In CHOP:** Reference to a MIDI In CHOP which should be a MIDI keyboard to enable further functionality.
    - **MIDI Scale Learn**: Can define a **Custom Scale** by hitting notes one-by-one on your MIDI In instrument.
    - **MIDI Live:** Hitting chords on your MIDI keyboard determine the Custom Scale in real-time! This lets you duet with your visuals!
        - **Live Octave Offset:** Offsets the octave of incoming MIDI notes. This is good if you are playing an external synthesizer/VST in parallel to this component as a duet.
        - **Sync Steps:** Syncs the number of steps to the number of incoming notes. This is not available when **Extend Mode** is on.

- **Randomize:** Randomize the order of notes.
    - **Seed:** Seed of the randomization.

## Output tab

- **MIDI Active:** Activates **MIDI Out CHOP.**
- **MIDI Out CHOP:** Reference to a **MIDI Out CHOP**, **directly**. No nulls or other operators after it, the **MIDI Out CHOP** itself!
- **Channel:** MIDI channel to output notes on.
- **VST Active:** Activates **VST CHOP.**
- **VST CHOP:** Reference to an **Audio VST CHOP**, **directly**. No nulls or other operators after it, the **Audio VST CHOP** itself!
- **Channel:** VST channel to output notes on.

## About tab

- **Author:** That is me! Function Store/Daniel Molnar/Dan *([@function.str](https://instagram.com/function.str); dan@functionstore.xyz).*
- **Install:** In case [m*ingus*](https://github.com/bspaans/python-mingus) does not install automatically you can try pulsing this.

# Output slots

*Note: The MIDI/VST output happens directly by referencing a MIDI/VST output CHOP. See in **Parameters.***

1. Sampled values in TOP representation
2. Sampled values in CHOP representation
3. Note triggers
4. Sample Preview output

# Additional Notes

- It is recommended that you run this in its own TD instance from your visuals, and run your VST in a DAW
- Be aware that your VST/MIDI will have a poly count and you can easily exceed that
- In **MIDI Live** mode be careful to release the notes at the same time. In general this can be improved.
- Check out the [Data Sonification](https://thenodeinstitute.org/courses/ss23-td-15-data-sonification/) workshop by Elekktronaut at The NODE Institute! This tools is also featured in his course.

# Contributors

- [mingus](https://github.com/bspaans/python-mingus): The python library providing the **Scale Modes** and musical notation interpretation.
- [AlphaMoonbase.Berlin](https://alphamoonbase.de/): Author of [td_pip](https://td-olib.org/component/td-pip), creator of [td-olib](https://td-olib.org).
- Beta Testers: [Elekktronaut](https://www.elekktronaut.com/), [kim0slice](https://www.instagram.com/kim0slice)
- Thank you to all my [Patrons](http://patreon.com/function_store) supporting my tool development!
- Thank you to Derivative for creating TouchDesigner!
