---
share_cop4600: "true"
site-folder: docs/Supplementary Material
---

Original Source:  [RAID 5 Array: Probability of Failing to Rebuild Array](https://superuser.com/questions/1334674/raid-5-array-probability-of-failing-to-rebuild-array)


It's not necessarily bad to configure your RAID 5 array with _N_ TB. What you need to worry about is how much data has to be read AFTER you lose a drive. Depending on the amount and size of the disks in your RAID 5 array, is what calculates your probability of failure for rebuilding the array.

> What is a URE?

A URE (Unrecoverable Read Error) is when reading the sector of a drive fails, and it cannot be fixed. A URE number simply means that _"on average 1 error is detected while reading n bits"_. In our scenario, it's simply running a risk that it will fail to be able to read one of the distributed parities while rebuilding the array.

> How to find out my probability of failure?

A typical consumer grade hard drive has a bit error rate of 10¹⁴. To calculate the probability, we would use the formula `Probability = 1-((X-1)/X)^R`. _X_ is the number of outcomes, and _R_ is the number of trials. In this case _X_ will be our bit error rate for the drive, 10¹⁴. _R_ will be the size of the RAID array after losing a disk. For this case that is 8 TB.

> Math Time!

First, we want to convert 8 TB to bits. This equals 6.4*10¹³. Now we can finally calculate the probability of our RAID 5 failing to rebuild after losing a disk.

Probability = 1-((10¹⁴-1)/10¹⁴)^(6.4*10¹³) ... plug this bad boy into wolfram alpha and you get 0.4727... multiply that by 100 and you have a 47% chance of your RAID 5 array failing to rebuilding in a 3x4TB setup that loses a drive. If you have a 4x4TB setup and you lose one drive, you have a 61% of the disks failing to rebuild.

> So should I risk it?

The overall conclusion is that if you are using consumer grade hard drives, it can be risky to use large drives with RAID 5. It's a much different story with enterprise grade drives and hardware. As an example, using a Seagate Enterprise drive with a bit error rate of 10¹⁵, recovering a 4x4TB RAID 5 array that loses a drive, has only a 9% chance of failing to rebuild. The consumer grade hard drives has a 61% chance of failure.

In response to a comment by Christoper, I found this sleek RAID rebuild failure chance calculator. It quickly and easily calculates the probability of a URE failing during a RAID 5 or RAID 6 rebuild.

> [RAID rebuild failure chance calculator](http://magj.github.io/raid-failure/) - created and maintained by [magJ](https://github.com/magJ)

    
- @ChristopherHostage I showed this post to some co-workers today because this topic came up. I somehow stumbled upon a Reddit post that shows [how to calculate the failure rate for Raid10 due to UREs](https://www.reddit.com/r/DataHoarder/comments/6i4n4f/an_analysis_of_raid_failure_rates_due_to_ures/) 
    
    – [DrZoo](https://superuser.com/users/482362/drzoo "10,431 reputation")
    
     [Nov 5, 2018 at 20:54](https://superuser.com/questions/1334674/raid-5-array-probability-of-failing-to-rebuild-array#comment2067346_1334694) 
    
- 
    @quantum [If a RAID5 system experiences a URE during rebuild, is all the data lost?](https://serverfault.com/questions/937547/if-a-raid5-system-experiences-a-ure-during-rebuild-is-all-the-data-lost) I believe that will answer your questions. 
    
    – [DrZoo](https://superuser.com/users/482362/drzoo "10,431 reputation")
    
     [May 8, 2019 at 21:20](https://superuser.com/questions/1334674/raid-5-array-probability-of-failing-to-rebuild-array#comment2165060_1334694)