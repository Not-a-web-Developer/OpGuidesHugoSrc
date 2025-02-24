+++
bookToC = false
+++
# An Interview With Loial Otter

**9/23/2018**

<img src="/nonfree/interviews/loial.jpg" alt="loial" height="200em">

**Introduction:**

I'm Matthew or LoialOtter, a crazy person up in western Canada who likes to make stuff. I work at Kron Technology as an engineer mainly in FPGA, electronics design and hardware design.

I don't have any credentials except a certificate in baking and pastry arts. I'm self-taught mostly due to not being ever able to afford going to school. I suggest others do when the have the opportunity but it isn't absolutely required.

**Obviously working at Krontech has been a super cool experience of yours that has probably dominated the majority of your recent work, but what projects that you've done outside of Krontech did you have the most fun working on or do you find the most interesting?**


My passions all lie around the crossover between digital and physical worlds. I love motion control and am absolutely thrilled when I can make some code move something or control something in the *real world*.

A few years ago I attempted to make my own 3d printer company and designed a motion platform that was rigid and quite a bit larger than what was available at the time. It was also very high speed to handle the greater build areas. Unfortunately the business ran out of money before I was able to get the unit to market; a mistake on my part. The excitement, though, of getting a machine partly built and get it working through it's own control loops is just... amazing.

I've done many other things both through work and out on my own; I've made PLCs cycle engines in cold weather to keep heavy equipment ready to run and operating in extreme conditions. I've hacked cars and reverse engineered the protocols in use to adjust the handling of all-wheel-drive systems. I've done heavy adjustments to my laser cutter, the small mill we have at my house and stuff like that as well.

The mill is another great example. The first thing I cut on it was the mounting brackets for it's own steppers to CNC it. I had the thing partly CNC'd within a week of getting the mill installed and fully working within three weeks (Z-axis was a little more tricky). I continued on to add a fourth axis and then rebuild the fourth axis using another way when the first one proved too weak.

The most interesting part of any project is getting through all the tough design challanges and getting the hardware proven. The least-liked, for me, is software. I dislike it enough that I've always been searching for a way to write scripts that do the tieing together of the internal parts and have finally found something that nearly covers what I want: micropython. I've been working on a software replacement for the Lerdge-X 3d printer control board, a cheap board from china that's surprisingly well built. It's running micropython but behind the scenes I want an interrupt driven lowlevel control system that'll manage the actual movement and thermal loops. This will allow me to quickly hack the board into what ever type of robot I want, use what ever control language I want and yet still have a reliable control loop that'll properly handle being starved for motion updates. You can follow that at <https://github.com/LoialOtter/micropython> and <https://github.com/LoialOtter/Open-Lerdge-X>

**What part of what specific project has been the most difficult and/or most rewarding thing you've ever worked on?**


Hard to say the most difficult and/or rewarding. There was a very good one when working on the all-wheel-drive system where due to a very small detail in the readout protection on STM32 devices we weren't able to replace the boot code on devices that were already shipped. It was a very troubling issue as the bootloader I had built was causing check-engine lights on very select vehicles (ones that I didn't test on). The issue was that the bootloader wasn't passing CAN traffic within the window given for connection before the final software started. The fix, of course, was to patch the new bootloader but due to the first 4KB being locked down and non-erasable without bricking the device, it wasn't possible to make a payload image that'd just reflash the CPU.

The solution was a little unusual but worked well enough. When stepping through the original bootloader I noticed that a function used to clear ram on the device was located outside the 4KB block over at about 7.6KB. I wrote a special C function that had inline assembly which reset the CPU manually to a known state, set up the interrupt table so it was at a different location and then fiddled the stack both so the return from the function would call reset and it would unwind the stack to zero when doing so.

It took about a month to find a solution, develop the return function and get through all the nitty gritty bits and testing. The result was updating the bootloader on all the devices in the field, fixing the issue as well as making the system a little more resiliant to similar problems.

**What in general as you work on something causes you the most issues- that is what reoccurring problems do you / have you had? This could be related to your field or how you work.**


Getting going on software. I have way too many projects that are sitting around with full hardware almost working but the inertia of getting going on the software is easily the most difficult hurdle even though it's often one of the simplest. The issue is sorta a form of analysis paralysis; deciding how software is to be laid out is one of those things that changes how software works for the entirety of it's life. The first few lines of code usually defines the rest of the whole thing and I've written the wrong first few lines so many times that it scares the crap out of me.

**What is your favorite 'hidden gem' tool, this could be software or hardware (or say both)**


MicroPython (not Circuitpython, sorry). It's totally OP in getting past that initial difficulty as long as you don't have to write underlying systems for it. On STM32 devices, especially, it's amazing as you have a USB interface that offers a filesystem as well as the REPL, a command line interpreter for python. The filesystem is where your code will end up once it's working. It's saved in source form and compiled on load to bytecode or machine code depending. The overall speed of it is amazing; the bytecode is fast and works with nearly everything within Python 3.4 while the machine code types are way faster but lack the ability to allocate new memory and such (so ints are a little weird - don't go past 28bits in machine code).

REPL is utterly amazing. It allows you to simply open up a console and start poking at the CPU. As there's a memory mapping system (machine.mem32/mem16/mem8) you're able to write drivers for hardware in python and just get the whole system up and running very quickly.

I've made a watch that's been shown some around including part of the interview on the Amp Hour a few months back. That watch runs micropython even to the point where the display is being driven by DMAs and PWMs both fully set up in python. The watch took a day to get all the software up and running on including making the drivers for the DMA, timers, the accelerometer on the unit and i2c parallel expander as well. By the end of the day I had the buttons all working, it showing the time when you tilted it to the right angles, even was doing the whole charge regulation and such.

**What is the most valuable lesson you've learned 'the hard way' when it comes to engineering**


When something's too complex to work on directly, you need to step back and decide if you should start from scratch. Often rewriting software or redesigning a board from scratch takes a tenth the time as you already know what the requirements are. You also know to a greater degree how it's supposed to work which you often don't know when you start a project. Sometimes there's a large loss of time due to it as you already spent so much time working on the thing but that's often similar to the gamblers fallacy; If you keep going at it long enough it'll get better and work better.

It needs to be assessed though because sometimes rewriting it will be far harder than just fixing it - this is a very difficult decision and always will be. With experience you'll get a better handle on how much effort will be needed for both rewriting and fixing.

**Every engineer faces the problem of feature creep in any project, how do you deal with this on things you work on?**


First off, make sure you start with a modular design. This is why the above comment that the first few lines are often the most important. If you make everything monolithic and all the parts talk to each other and are woven together, you will have a hell of a time designing for every possible addition later. The other benefit is that given a modular design you can look at all the shiny features you want and realize that you can implement them later - they aren't important now. You can keep features from entering the design docs because the design docs should be the base parts to get the system going.

You can extend the design with all the cool features you want by documenting those features seperately and working on them as you need/want rather than throughout the whole process. If you need a break from the core of the project, you can take on one of those features that normally would be considered feature creep and work on them to keep you from going mad doing just the core (I have ADHD; this is one of my coping mechanisms).

**Is there any major skill you wish you had developed in formal education that you didn't?**


As I don't have formal education outside an unrelated field (ask me some time about how related it is to electronics though); I don't have a direct answer. I really feel that my project management and time management abilities would be far greater had I gone through formal education. I feel that engineering university, though great at giving you a good core of general knowledge that skims over most parts of engineering, isn't really there to teach you that. It's there to teach you time management and how to deal with crisis and priorities. The skills of being able to assess what's absolutely critical right now to work on because you only have time to finish the work for one out of three classes and you have a final coming... that's amazing skills for when you're getting near release and you've got a product almost working with four or five parts that are critical but you don't have time to complete them all. What is going to make the user-base the most happy and do the best job fulfilling the requirements of the product. Are there parts that you can postpone and fulfill later?

Unlike at university, real life has fuzzier time limits on things. Understanding that you can also trade off getting things done with how late you'll be on development is also key; this is something I entirely didn't know or understand when I was working on the 3d printer business. If you delay too long and have taken pre-payment, you may have to pay some people back and have extremely negative press from those people - getting a product out that does 90% of what they need and offer to get the last 10% done over the next period may be way better than delaying four or six more months. Also be prepared to check over everything, the other edge of such a move is that you didn't fully vet the design out and may either have to break a promis to include a feature or to recall the product, a nightmare nobody wants to do.

Most of those skills are in assessing situations and making critical decisions of what's important and what's not. School teaches these as a side-effect of their format. Even the point of money for schooling is often an eye opener: you may be able to cook everything for yourself, but how much are you loosing for time to do your work... is that important enough to be a reasonable trade off? How can you manage your finances so when those crunch times come you'll be ready for needing more lunch money to stay afloat.

**Is there any skill that comes to mind you spent to much time developing that you wish you hadn't?**


Far too many things to count. As I think I mentioned I'm poor at time management. A number of times I've become too engrossed in things and don't realize that I've led myself into a dead end in development. There are plenty of times I've had to step back and realize that I could have spent that time much better going through and defining the problem. My 3d printers are a great example of it took too long to develop and I screwed myself over on it because of that.

Another major trap of getting too deep like that is you'll sometimes find yourself partway, looking like you got the hardest part done then figure out what you did, though tough, was just a fraction of what was needed for the next part. I have an FPGA debugger block that fits this; I have the verilog all written and overall it looks good but as soon as I went to start testing it I hit a major problem; I have to get the software working that'll read out the data given. The real problem is that I have to write those programs in C/C++ and cross-compile them for the target and due to complexity involved that's going to be a hellish thing. What I really needed was a scripting environment on the target and... well... the whole debugging system is on hold until we get the camera upgraded so I can get scripts running on it...

**Getting away from engineering for a bit, What do you do for fun?**

More engineering!

Oh wait, other than that... probably more designing...

Okay fine; I like to go camping and on road trips and travelling and such. I love going to conventions. Most of my disposable income seems to be eaten up by personal projects, though, so I tend to just build more stuff on the side.

**Any good books, movies, TV shows, etc. You'd recommend?**

I recommend stop watching TV; If you like a series, wait until the whole series is finished then buy/aquire it and watch it all at once - so much easier to get work done when you're not always distracted by what's going to happen next or all the silly ideas that tv/movies/etc. give you... or at least the ones they give me.

I use movies as a drug of sorts. I get a rush due to the stimulus response and fanale of them. After a movie I'm working off that high for a while so I have to make sure that after movies I can stay up late and get work done. The actual movie isn't very important though stuff like Deadpool is great.

**If you could meet anyone, living or not, who would it be and why?**


There are a few people I would love to meet and talk to: Chris Hadfield, Elon Musk, Peter Mansbridge (Moosebridge, heheehe). Top of that list is definitely Chris Hadfield though; his experience, his attitude towards things, he's so nice... and he's so smart. Runner up would be Peter Mansbridge; he's again extremely smart and has a tun of experience.

**Assuming you can talk about it, what's the biggest 'Oh Shit' moment you've ever had, be it one you caught on time or not? (I'm particallarly interested in the 'almost did something lethal' scenarios)**


First one would be the bootloader mess-up earlier.

Another great example would be the time I was going and trying to repair a VFD (Variable Frequency Drive) and decided that, since I couldn't see the signal right, attaching the negative side of the scope to the negative rail on the input rails of the VFD was a good idea. I had the thing powered up and was connecting the ground side aligator clip. The next moment I realised there was now a blind spot in the middle of my vision, my hearing was ringing from the bang the 'gator clip made when it vaporized and my hand was strangely warm. It took a few moments to realize what had happened after pulling what was left of the clip away; I realized my hand was now electroplated with what was the aligator clip. I had shot -280V through the ground side of the probe and was lucky as hell. The end result was that most of my vision returned within a few minutes, a small amount of it took about two days to recover (or my brain to blot out the darkened spot). The probe I was using lost it's dc coupling so I must have vaporized something internal to it; luckily the scope was fine.

**Because of the engineer anti-social stereotype, I'm curious, do you think we as a species have been negatively or positively impacted by the way the Internet and social media has changed how people interact?**


Both, heavily. Things like twitter and other social media systems as well as the near-anonymous commenting on things like youtube have made it seemingly encouraged to troll and/or abuse people. Without a face attached it's easier for people to do absolutely shitty things to others.

At the same time, global communication makes doing high tech stuff possible. I learned electronics mainly through the internet and through communication with people who were hardly ever in my own province or country let alone my own town. I have more friends now than I did at almost any other point in my life with the only other time being when I was helping with conventions and even that was entirely because of the internet.

**You expressed on your interview with the AmpHour that you developed your VFD pendent as a method to help you with ADHD, how has that been working for you?**

If I hadn't accidentally vaporized some traces on one of the boards, fantastic. The watch pendant was doing the job of reminding me to pay attention to if I needed to eat or drink and to check what I was working on to make sure it was effective. Since it's no longer working as of a few weeks ago, that has been a really big problem again. I really need to make a new revision and possibly bodge together another test one so I have one again.

As I'm sure this'll be asked, the watch lasted about 6 months of hell being worn. When moving some boxes I had previously crushed a few of the buttons and had to adjust the code so the damaged buttons weren't either always pressed or draining the battery due to being shorted to ground (changed the pull-up to a pull-down on the input and ignored the input). I had drained the battery to zero volts a few times which had also impacted battery life but not terribly, it still got about 4-6 days off each charge.
The thing that did it in was a partial short on the vibration board to GND which caused the battery to empty and worse, the current limit to keep the cell from charging. While trying to resurect the device, a process of using some sharp probes on a multimeter to jump the dropper resistor past the analog switch used for charging so it could get enough initial charge to boot the CPU and get things operating correctly again, I inadvertently shorted to another point and vaporized one of the traces going to the battery (LiFePO4 has an amazing current capability, it can easily vaporize traces if given the oportunity).

**Who are your biggest inspirations?**

My mom and oldest brother.
My mom took a job when I was quite young teaching early childhood education in northern bc. It was a small program and the position didn't pay great but was better than most of the oportunities down on the coast. While growing up I watched her take this tiny little program that was sharing a chemistry lab in the college and grow it to cover most of western canada. She developed distance education systems back when the internet was just starting to reach northern bc. She had so many people telling her that it was impossible to make distance education work and that the program would never ammount to anything and rather than just arguing, she took their issues, studied and understood them and engineered the course system to work around or even embrase some of the issues to flip them on themselves to improve the whole system.

My brother taught me algebra or the beginnings of it in kindergarten. He taught me most math functions in advance of school and mentored me in programming. He did things with the early computers such as 3d engines that I didn't even know were difficult until later when I started looking at what was possible.

There are, of course, others who were great inspirations though often in odd ways; I love Sherlock Holmes, for instance, because he is intelligent and understanding enough to know that lawfullness is not always the best coarse of action when solving things. I also strive to be like Horratio Hornblower (look up the BBC production, it's amazing... and i so should've listed this above)

**What creators (youtube, twitters, etc) do you think are way to underrated and deserve a shout out?**

* Tesla500; damn, I work for him...
* This Old Tony is awesome; absolutely love his humor and what he does is great.
* Marco Reps; again, great humor. He does tend to sell things a lot but hey, the content is still pretty good.

**What's the best advice you've ever received? Worst?**

Best:

Those without dirty hands are wrong or at least assume that. Nothing will ever be perfect, aim for it but don't expect to hit it and you'll be great when you get 3/4 of the way there.

Worst:

Hmm... that's much more difficult as I tend to forget most advice. Oh, I know, that guy who once told me not to bother making my own stuff because people have made so much better than everything I could make.

**What would be your perfect work environment?**

It'd contain a nice kitchen with a gas range. It'd have a hammock with some monitor mounts around it so I can still get my multi-monitor setup. It'd also have tools for everything from CNC milling through electronics assembly.

**How do you stay motivated to keep doing cool things?**

Do small cool things even when you don't feel like it; It won't always work but hey, sometimes it does.
Watch single-episode tv shows if you're really stuck, or go watch a movie... use the internal drugs from the movie to motivate.
Hug your mate/partner... eh, totally doesn't help in the short run but they will more than you can imagine.
When your stuck, don't just sit there and stare at it, go do something else for a bit (don't invest too much time though, you still want to come back to this one)
Sitting around and BSing about stupid ways of doing things is great brainstorming

Also don't sweat the days you can't fullfill a 120% like you normally drive for; that's fine. If it goes on too long, do something you can't ignore to remind you to do stuff... haircut, paint your nails some weird colour, whatever... what ever will give you that reminder that is hard to ignore. That's what's saved me a few times (Minecraft, I'm looking at you!).
