# sws_muscle_bc-tool
## IO Table
IN1.2\
HIGH - 下顎を開く指令. Command to open the lower winding guide jaw.\
LOW - なし。None.

IN2.2\
HIGH - バンド装着一連動作 バンド成形開始➡バンド端挿入➡
バンド締付➡バンド切断/廃棄. Start the band attachment cycle  (band forming, band insertion, band tensioning, band cutoff disposal)\
LOW - なし。None.

IN3.2\
HIGH - リセットサイクル開始 バンドホルダーを原点に戻す. Start the resetting cycle (Reset band holder to home position)\
LOW - なし。None.

IN4.2\
HIGH - 下顎を閉じる指令. Command to close the lower winding guide jaw\
LOW - なし。None.

## Changelog
260724-1356
1. Changed RESET behavior - require reset after band attachment procedure.
2. Reduce waiting time of homing process when transitioning from motor 1 to motor 2.
3. Added some logic banks to improve safe mode stability.

260723-1706
1. After IN2.2 process is completed, require the PLC to send a RESET command at IN3.2.

260715
1. k_parameters.txt\
1.a. Disable auto homing functions of motors 1, 2, 3 after machine power up. This should reduce risk of band holder crashing into the lower jaw mount.\
1.b. Set logic bank cycle time to 100 ms for all motors. An increased polling rate decreases the likelihood of missing signals from the PLC and lower jaw sensor.\
1.c. Slow down all motor speed settings for now to 70% of actual for safety. Set by parameter k6.

2. logic_banks.txt\
2.a. L1.2 Now turns on O1.2 upon starting up all motors successfully. Program then jumps to L2.2.\
2.b. L2.2 now handles IO interfacing with PLC through IN1.2 to IN4.2 and O1.2 to O2.2.

3. motion_banks.txt\
3.a. Added homing commands for motors 1, 2, 3 in B4.1. Added some conditions to reduce the risk of having the band holder crash against the lower jaw mount.\
3.b. Set K30. = 0. Homing is NOT required before staring machine operation. Homing, and the necessary safety measures must be done on the PLC side.\
3.c. B1.1 now opens the lower guide jaw by rotating the lower jaw cam until the lower jaw sensor state is HIGH. Does nothing if lower jaw sensor state is already HIGH when B1.1 is executed.\
3.d. B2.1 now closes the lower guide jaw by rotating the lower jaw cam until the lower jaw sensor state is HIGH. From here, the lower jaw cam rotates a litte more until the lower jaw closes.\
3.e. In addition to 3.d., it is important to note that if B2.1 (close jaw command) while the jaw is closed, it will still rotate the lower jaw cam until the lower jaw sensor is HIGH (meaning the lower guide jaw is open) - after which it will rotate a little more to finally close the lower jaw. This is to help make the home / closed position of the lower jaw more determinable. Also, is reduces the risk of mis-timing the nipper action.

4. data.txt\
4.a. Added a delay timer T1.2 = 3000 during machine startup before setting O1.2 to high.