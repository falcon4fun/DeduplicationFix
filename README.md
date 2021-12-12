# DeduplicationFix
Possible fix for 0x80565323 error and FSCTL_DEDUP_FILE.DEDUP_SET_CHUNK_STORE_CACHING_FOR_NON_CACHED_IO failed with ERROR_INVALID_FUNCTION for volume.

Found sometimes deduplication getting broken on Windows. In example BSOD or sudden reboot while working on jobs (scrubbing and etc):
1. DataAccess sets false (disabled).
2. Trying to enable it with Enable-DedupVolume \<Letter\>: -DataAccess throws error: 0x80565368
3. fltmc instances shows there is no Dedup filter for required volume
4. Reinstalling Dedup don't help
5. Cannot unoptimize volume it cannot enable DataAccess.
  
Investigated and found solution:
1. Get access to "System Volume information\Dedup\Settings".
2. Backup HSM*.cfg files (possibly VolumeJobLock.bin too)
3. Remove them.
4. Reboot computer.
5. ~~Check if Disable-DedupVolume -DataAccess and then Enable-DedupVolume -DataAccess works fine.~~
Don't use "-DataAccess" parameter. It's buggy for me. Remove HSM*.cfg files. Enable dataaccess. Remove file them again. And forget about it.
I've found that disabling dataaccess, enabling again, disabling and enabling again breaks dat shit again.
It's like normal CFG file size: 77 bytes. Broken CFG file size: 120+ bytes.
7. Check logs for permission errors.
8. Launch some jobs (optimization, garbagecollection, scrubbing) and check logs again.
   
![This is an image](https://i.imgur.com/7kKpoZb.png)

If you don't reboot your machine and try to enable data access, deduplication will be broken again after next reboot.
  
Some links over internet with no solution with similar problem:
https://docs.microsoft.com/en-us/answers/questions/284192/deduplication-fails-on-general-use-file-server-wit.html
