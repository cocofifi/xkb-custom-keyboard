# xkb-custom-keyboard
Modified files to be able to customize keyboard layout on modern Linux desktop environments


## Custom keyboard tweaks in 2017 for Ubuntu

Original source (blog post) - https://aboutfoto.wordpress.com/2017/04/23/custom-keyboard-xkb/

Linux for the desktop changes with every year going by. So does the ability to change the keyboard layout. For many years, xmodmap was the way to go, if a Linux user wanted to alter the configuration of his or her keyboard. This is what I did, a few years ago, when I slightly altered my FRench keyboard layout to invert A with Q, J with O and K with E as described in one of my previous posts: Custom keyboard layouts on Linux and the jokeft version.

If wondering why such a change would be of any benefit at all, here is the heatmap of using the virtual keyboard of an Android phone after about 3000 words typed in a mix of French, Romanian and English (as provided by SwiftKey):

![Typing_Heatmap_3000words](https://aboutfoto.files.wordpress.com/2017/04/typing_heatmap_3000words.png)

It is easy to see that A sees more use than Q, and that K and J are much less frequently used than E or O. Now, how can we change the keyboard layout on Linux, when xmodmap is no longer an option ?

It is both easy and complicated. Easy, because we only need to alter two text files, and complicated, because the alterations require reading and understanding, at least partially, what the manager of the keyboard, xkb, does. Of great help for the tweaks I describe was a post by Michał Kosmulski (http://michal.kosmulski.org/computing/articles/custom-keyboard-layouts-xkb.html).

The recipe was only tested on Ubuntu 16.04 and Ubuntu 17.04, and if you use another Linux system, the path to the files to modify might be different. In my case, I modified the file that contains the correspondence between the physical keyboard and the characters that are sent to the system through xkb. For my french keyboard, all the different layouts are found in the fr text file in the folder:

/usr/share/X11/xkb/symbols/

Make sure to keep a copy of the original file somewhere, if something goes wrong with the tweaks. Since I was only making small changes to the layout, I modified the original file by adding the following section at the end:

// My own keyboard changes
partial alphanumeric_keys
xkb_symbols "latin9_jokeqa" {

    // Modifies the basic fr-latin9 layout to invert aq, ek, oj

    include "fr(latin9)"

    name[Group1]="French (legacy, alternative, invert aq, ek, oj)";
    
    key     { [   q,    Q,  acircumflex,    adiaeresis ] };
    key     { [   k,    K,     EuroSign,    cent ] };
    key     { [   j,    J,  ocircumflex,    odiaeresis ] };

    key     { [   a,    A,  Acircumflex,    Adiaeresis ] };
    key     { [   o,    O,  Ucircumflex,    Udiaeresis ] };
    key     { [   e,    E,  Icircumflex,    Idiaeresis ] };
};

**Somehow, copy paste from the browser perturbs the text files, please use the raw text files from the repository.**

The rows of the keyboard are A, B, C, D, from the space bar row, so that AD01 corresponds to the first character key in the fourth row (a, on the azerty French keyboard). To get an idea about what this layout change does (colored pairs correspond to the modified keys):

![frmodifiesvg](https://aboutfoto.files.wordpress.com/2017/04/frmodifiesvg.png?w=809)

The unique identifier of this layout is xkb_symbols "latin9_jokeqa"

The name[Group1] value is important and describes the keyboard layout, as it will be seen by the different GUIs for regional settings in GNOME, KDE or XFCE, here “French (legacy, alternative, invert aq, ek, oj)”.

Once the layout was defined, it needs to be referred in an xml description file, called evdev.xml, situated in a different folder:

/usr/share/X11/xkb/rules/

Here, we need to insert the information that was specific to the newly defined tweak to the keyboard layout in the following form:

<variant>
 <configItem>
 <name>latin9_jokeqa</name>
 <description>French (legacy, alternative, invert aq, ek, oj)</description>
 </configItem>
</variant>

Once the new files were saved in the original locations (as sudo), I’m not sure that the following command is required, but it could help:

sudo dpkg-reconfigure xkb-data

It helps also to reboot the system, because without a reboot, the keyboard indicator did not recognize the two versions of the keyboard.

![language_keyboard](https://aboutfoto.files.wordpress.com/2017/04/language_keyboard.png?w=400&h=219)

That’s it. Compared with the xmodmap method, this one is persistent and works by default in KDE, GNOME and XFCE. It is also somewhat futureproof, as it should work with the Wayland protocol.
