GlusterFS 本身并非真正的文件系统。它将现有文件系统连接成一个（或多个）大块，以便写入或读取 Gluster 的数据可以同时分布在多个主机上。
# gluster volume info
```
$ gluster volume info
Volume Name: 8b8974a3_vs_vol_rep2
Type: Distributed-Replicate
Volume ID: 4d709e9f-edb2-44b0-9f9c-a48d1fa1e119
Status: Started
Xlator 1: BD
Capability 1: offload_copy
Capability 2: offload_snapshot
Number of Bricks: 24 x (2 + 1) = 72
Transport-type: tcp
Bricks:
Brick1: host-905d7c6aaec5:/sf/data/vs/local/sc0sVv-SxkR-dubc-3W7L-1fhf-6Bcb-vxlLCX/4c87f7b8-9996-11ea-bf37-905d7c6aaec5
Brick1 VG: sc0sVv-SxkR-dubc-3W7L-1fhf-6Bcb-vxlLCX
Brick2: host-78aa8236cac0:/sf/data/vs/local/aF0Xjg-ATFL-2Nq1-PZH8-CtBw-vczF-l7ADzj/4c7722c6-9996-11ea-9adc-78aa8236cac0
Brick2 VG: aF0Xjg-ATFL-2Nq1-PZH8-CtBw-vczF-l7ADzj
Brick3: host-542bde5ee826:/sf/data/vs/local/LJJnEf-UnoA-MaQg-hRPp-nWXq-GYH1-bDa2I6/6fda8898-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick4: host-905d7c6aaec5:/sf/data/vs/local/hDflfc-oe5j-ieoh-ONv9-Ncdh-luVd-OxjDL9/4c82a0b0-9996-11ea-bf37-905d7c6aaec5
Brick4 VG: hDflfc-oe5j-ieoh-ONv9-Ncdh-luVd-OxjDL9
Brick5: host-78aa8236cac0:/sf/data/vs/local/Ld0cs5-Sguc-Zv9s-g10b-z2ic-VJVS-lXxetp/4c7852d6-9996-11ea-9adc-78aa8236cac0
Brick5 VG: Ld0cs5-Sguc-Zv9s-g10b-z2ic-VJVS-lXxetp
Brick6: host-542bde5ee826:/sf/data/vs/local/PeKbPj-zwqJ-YQfx-baVO-8as8-l7Ge-VOMXLp/6fdb4922-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick7: host-905d7c6aaec5:/sf/data/vs/local/0PlypZ-jfs4-Ux3y-Tacq-bnVc-ctNK-DFpo6d/859df98a-2cb2-11f0-96f0-1c34da5c1199
Brick7 VG: 0PlypZ-jfs4-Ux3y-Tacq-bnVc-ctNK-DFpo6d
Brick8: host-78aa8236cac0:/sf/data/vs/local/8Hti4K-CI77-s5Tp-6jdR-kpED-qcns-gKLYdp/4c98022a-9996-11ea-9adc-78aa8236cac0
Brick8 VG: 8Hti4K-CI77-s5Tp-6jdR-kpED-qcns-gKLYdp
Brick9: host-542bde5ee826:/sf/data/vs/local/HkxM1t-cxHe-B5pm-qZ3t-kffm-A9Ng-GMAVkS/6fdbe5a8-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick10: host-905d7c6aaec5:/sf/data/vs/local/5xSUYh-Knup-VstB-XC1l-0aML-poQ4-FbUaLX/4c7e1630-9996-11ea-bf37-905d7c6aaec5
Brick10 VG: 5xSUYh-Knup-VstB-XC1l-0aML-poQ4-FbUaLX
Brick11: host-78aa8236cac0:/sf/data/vs/local/53T0AS-7X3s-hZfz-OehZ-Bpje-ojfc-kK315X/4c772b5e-9996-11ea-9adc-78aa8236cac0
Brick11 VG: 53T0AS-7X3s-hZfz-OehZ-Bpje-ojfc-kK315X
Brick12: host-542bde5ee826:/sf/data/vs/local/re7Afn-dea8-CGT9-fU1Q-6uUP-RLu2-2eoZ94/6fdc7c5c-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick13: host-78aa8234addf:/sf/data/vs/local/iyqv69-x4RL-wbvk-bux2-FaDn-BX8g-mne762/4d3cd93a-9996-11ea-acb6-78aa8234addf
Brick13 VG: iyqv69-x4RL-wbvk-bux2-FaDn-BX8g-mne762
Brick14: host-78aa8234a6e3:/sf/data/vs/local/MjfY1T-e4yD-1n1e-jSl0-i1fg-BHMh-T846j6/4c6b7fd4-9996-11ea-b7f2-78aa8234a6e3
Brick14 VG: MjfY1T-e4yD-1n1e-jSl0-i1fg-BHMh-T846j6
Brick15: host-78aa8234a6d7:/sf/data/vs/local/mQLhg8-lXKk-AWOR-zfHA-FdiJ-ajAm-KJWn3V/6fdd14aa-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick16: host-78aa8234addf:/sf/data/vs/local/i1yYLt-j2jC-Uyx3-J30P-gkQ1-OEFI-RUzjnj/4c80d3ca-9996-11ea-acb6-78aa8234addf
Brick16 VG: i1yYLt-j2jC-Uyx3-J30P-gkQ1-OEFI-RUzjnj
Brick17: host-78aa8234a6e3:/sf/data/vs/local/K1T56F-6teW-yWJa-odXo-Xciz-p5VE-RhHGfZ/4c7c6916-9996-11ea-b7f2-78aa8234a6e3
Brick17 VG: K1T56F-6teW-yWJa-odXo-Xciz-p5VE-RhHGfZ
Brick18: host-78aa8234a6d7:/sf/data/vs/local/UVnv6J-fOmK-qetj-pPQM-pfnK-DqJw-QMrlri/6fdd7440-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick19: host-78aa8234addf:/sf/data/vs/local/bIeBZ2-0VO4-86dB-Sci1-cl1q-X32G-sFjRcj/4c79f9ce-9996-11ea-acb6-78aa8234addf
Brick19 VG: bIeBZ2-0VO4-86dB-Sci1-cl1q-X32G-sFjRcj
Brick20: host-78aa8234a6e3:/sf/data/vs/local/3BM2x6-WygF-cXn6-ZbS9-WeT5-ZLOH-tDHSP1/4c6b426c-9996-11ea-b7f2-78aa8234a6e3
Brick20 VG: 3BM2x6-WygF-cXn6-ZbS9-WeT5-ZLOH-tDHSP1
Brick21: host-78aa8234a6d7:/sf/data/vs/local/9KBFqX-kjuo-LihM-GGrP-jGFh-qcpV-4m8mBQ/6fddd0de-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick22: host-78aa8234addf:/sf/data/vs/local/GkDmju-Xej7-K7Qf-iRQf-SUy1-ZgVf-WzFglz/4c6e0182-9996-11ea-acb6-78aa8234addf
Brick22 VG: GkDmju-Xej7-K7Qf-iRQf-SUy1-ZgVf-WzFglz
Brick23: host-78aa8234a6e3:/sf/data/vs/local/2Xo0M8-ovB3-A1GO-5qKe-omU8-RHDA-vNrA1A/58569aea-9996-11ea-b7f2-78aa8234a6e3
Brick23 VG: 2Xo0M8-ovB3-A1GO-5qKe-omU8-RHDA-vNrA1A
Brick24: host-78aa8234a6d7:/sf/data/vs/local/4cHibt-ZJv0-PbOw-xKfj-R7yS-9bAG-DNK0Ic/6fde2282-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick25: host-542bde60e6aa:/sf/data/vs/local/yhhsaQ-YJgV-wsn7-iVBr-Krp1-aYs4-5XQYeR/4c76aa6c-9996-11ea-8172-542bde60e6aa
Brick25 VG: yhhsaQ-YJgV-wsn7-iVBr-Krp1-aYs4-5XQYeR
Brick26: host-78aa8236cacc:/sf/data/vs/local/yBIQUc-mfGU-cOcs-ZopN-zbyp-3KC7-ZAh25J/4c7aebae-9996-11ea-b2a5-78aa8236cacc
Brick26 VG: yBIQUc-mfGU-cOcs-ZopN-zbyp-3KC7-ZAh25J
Brick27: host-78aa8234a6e3:/sf/data/vs/local/2Xo0M8-ovB3-A1GO-5qKe-omU8-RHDA-vNrA1A/6fde7570-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick28: host-542bde60e6aa:/sf/data/vs/local/7xopQw-188v-jHhf-vxtG-M84W-fe2p-mq4237/4c76a60c-9996-11ea-8172-542bde60e6aa
Brick28 VG: 7xopQw-188v-jHhf-vxtG-M84W-fe2p-mq4237
Brick29: host-78aa8236cacc:/sf/data/vs/local/ikDMga-jQRN-8nYG-Tbfv-DAbp-Wfg9-h6IUQp/4c77cbae-9996-11ea-b2a5-78aa8236cacc
Brick29 VG: ikDMga-jQRN-8nYG-Tbfv-DAbp-Wfg9-h6IUQp
Brick30: host-78aa8234a6e3:/sf/data/vs/local/3BM2x6-WygF-cXn6-ZbS9-WeT5-ZLOH-tDHSP1/6fdec7b4-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick31: host-542bde60e6aa:/sf/data/vs/local/26kVS0-AVlZ-pI0r-yCOp-uTO4-7NdL-r712xW/4c76a788-9996-11ea-8172-542bde60e6aa
Brick31 VG: 26kVS0-AVlZ-pI0r-yCOp-uTO4-7NdL-r712xW
Brick32: host-78aa8236cacc:/sf/data/vs/local/LWvAYT-Pyia-K4yA-uQSp-sqgB-lDF9-JNcfhM/4c7fe2c6-9996-11ea-b2a5-78aa8236cacc
Brick32 VG: LWvAYT-Pyia-K4yA-uQSp-sqgB-lDF9-JNcfhM
Brick33: host-78aa8234a6e3:/sf/data/vs/local/MjfY1T-e4yD-1n1e-jSl0-i1fg-BHMh-T846j6/6fdf1b1a-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick34: host-542bde60e6aa:/sf/data/vs/local/09U8Cc-kcbZ-EqPm-ppKf-JSPk-3NZb-EwPitz/4c76a6de-9996-11ea-8172-542bde60e6aa
Brick34 VG: 09U8Cc-kcbZ-EqPm-ppKf-JSPk-3NZb-EwPitz
Brick35: host-78aa8236cacc:/sf/data/vs/local/4HOOS1-kGc5-RKIb-4NEB-wEeQ-9ACj-GTjTpN/4c77cf1e-9996-11ea-b2a5-78aa8236cacc
Brick35 VG: 4HOOS1-kGc5-RKIb-4NEB-wEeQ-9ACj-GTjTpN
Brick36: host-78aa8234a6e3:/sf/data/vs/local/K1T56F-6teW-yWJa-odXo-Xciz-p5VE-RhHGfZ/6fdf740c-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick37: host-78aa8234b10f:/sf/data/vs/local/zNix3T-gy3c-nL7g-0UX8-jT3U-8DVX-N1wX1r/4c960240-9996-11ea-8a2a-78aa8234b10f
Brick37 VG: zNix3T-gy3c-nL7g-0UX8-jT3U-8DVX-N1wX1r
Brick38: host-78aa8234a779:/sf/data/vs/local/vpSgWv-k1bK-6PH3-9Q0P-ojG7-11M7-R0aVjy/5b680b42-9996-11ea-8da3-78aa8234a779
Brick38 VG: vpSgWv-k1bK-6PH3-9Q0P-ojG7-11M7-R0aVjy
Brick39: host-542bde60e6aa:/sf/data/vs/local/09U8Cc-kcbZ-EqPm-ppKf-JSPk-3NZb-EwPitz/6fdfc4a2-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick40: host-78aa8234b10f:/sf/data/vs/local/spqkAb-hEwy-DeqA-wGhW-7RIr-ft6L-PRVIyi/4c950250-9996-11ea-8a2a-78aa8234b10f
Brick40 VG: spqkAb-hEwy-DeqA-wGhW-7RIr-ft6L-PRVIyi
Brick41: host-78aa8234a779:/sf/data/vs/local/rIAfjL-wj1r-lC31-zh0H-Pgli-5plg-0TajiR/4c7b7cfe-9996-11ea-8da3-78aa8234a779
Brick41 VG: rIAfjL-wj1r-lC31-zh0H-Pgli-5plg-0TajiR
Brick42: host-542bde60e6aa:/sf/data/vs/local/yhhsaQ-YJgV-wsn7-iVBr-Krp1-aYs4-5XQYeR/6fe0175e-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick43: host-78aa8234b10f:/sf/data/vs/local/WEfzqv-Oy2a-dzd4-MvKA-VVdD-p03w-4nEKa0/4c94fb02-9996-11ea-8a2a-78aa8234b10f
Brick43 VG: WEfzqv-Oy2a-dzd4-MvKA-VVdD-p03w-4nEKa0
Brick44: host-78aa8234a779:/sf/data/vs/local/dXRr8R-Z1DI-xZFR-Pleo-00TC-ydEr-FkAaYK/4c8e9b04-9996-11ea-8da3-78aa8234a779
Brick44 VG: dXRr8R-Z1DI-xZFR-Pleo-00TC-ydEr-FkAaYK
Brick45: host-542bde60e6aa:/sf/data/vs/local/7xopQw-188v-jHhf-vxtG-M84W-fe2p-mq4237/6fe069fc-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick46: host-78aa8234b10f:/sf/data/vs/local/DAc32A-12S1-nqcU-3V02-prx2-1suw-hxgOiy/4ca1bcc0-9996-11ea-8a2a-78aa8234b10f
Brick46 VG: DAc32A-12S1-nqcU-3V02-prx2-1suw-hxgOiy
Brick47: host-78aa8234a779:/sf/data/vs/local/ZD939o-rxdw-FIqF-ObaN-XoLd-Ibb2-Xg1fLT/4c7bfe86-9996-11ea-8da3-78aa8234a779
Brick47 VG: ZD939o-rxdw-FIqF-ObaN-XoLd-Ibb2-Xg1fLT
Brick48: host-542bde60e6aa:/sf/data/vs/local/26kVS0-AVlZ-pI0r-yCOp-uTO4-7NdL-r712xW/6fe0baa6-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick49: host-78aa8234a6d7:/sf/data/vs/local/mQLhg8-lXKk-AWOR-zfHA-FdiJ-ajAm-KJWn3V/4c825628-9996-11ea-98c0-78aa8234a6d7
Brick49 VG: mQLhg8-lXKk-AWOR-zfHA-FdiJ-ajAm-KJWn3V
Brick50: host-542bde5ee826:/sf/data/vs/local/re7Afn-dea8-CGT9-fU1Q-6uUP-RLu2-2eoZ94/4c7c1f7e-9996-11ea-8e87-542bde5ee826
Brick50 VG: re7Afn-dea8-CGT9-fU1Q-6uUP-RLu2-2eoZ94
Brick51: host-78aa8236cacc:/sf/data/vs/local/LWvAYT-Pyia-K4yA-uQSp-sqgB-lDF9-JNcfhM/6fe11578-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick52: host-78aa8234a6d7:/sf/data/vs/local/UVnv6J-fOmK-qetj-pPQM-pfnK-DqJw-QMrlri/4c81cee2-9996-11ea-98c0-78aa8234a6d7
Brick52 VG: UVnv6J-fOmK-qetj-pPQM-pfnK-DqJw-QMrlri
Brick53: host-542bde5ee826:/sf/data/vs/local/PeKbPj-zwqJ-YQfx-baVO-8as8-l7Ge-VOMXLp/4c7a942e-9996-11ea-8e87-542bde5ee826
Brick53 VG: PeKbPj-zwqJ-YQfx-baVO-8as8-l7Ge-VOMXLp
Brick54: host-78aa8236cacc:/sf/data/vs/local/ikDMga-jQRN-8nYG-Tbfv-DAbp-Wfg9-h6IUQp/6fe165aa-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick55: host-78aa8234a6d7:/sf/data/vs/local/9KBFqX-kjuo-LihM-GGrP-jGFh-qcpV-4m8mBQ/4d404f70-9996-11ea-98c0-78aa8234a6d7
Brick55 VG: 9KBFqX-kjuo-LihM-GGrP-jGFh-qcpV-4m8mBQ
Brick56: host-542bde5ee826:/sf/data/vs/local/LJJnEf-UnoA-MaQg-hRPp-nWXq-GYH1-bDa2I6/4c958e50-9996-11ea-8e87-542bde5ee826
Brick56 VG: LJJnEf-UnoA-MaQg-hRPp-nWXq-GYH1-bDa2I6
Brick57: host-78aa8236cacc:/sf/data/vs/local/yBIQUc-mfGU-cOcs-ZopN-zbyp-3KC7-ZAh25J/6fe1b4ba-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick58: host-78aa8234a6d7:/sf/data/vs/local/4cHibt-ZJv0-PbOw-xKfj-R7yS-9bAG-DNK0Ic/4c8156e2-9996-11ea-98c0-78aa8234a6d7
Brick58 VG: 4cHibt-ZJv0-PbOw-xKfj-R7yS-9bAG-DNK0Ic
Brick59: host-542bde5ee826:/sf/data/vs/local/HkxM1t-cxHe-B5pm-qZ3t-kffm-A9Ng-GMAVkS/4c7aff54-9996-11ea-8e87-542bde5ee826
Brick59 VG: HkxM1t-cxHe-B5pm-qZ3t-kffm-A9Ng-GMAVkS
Brick60: host-78aa8236cacc:/sf/data/vs/local/4HOOS1-kGc5-RKIb-4NEB-wEeQ-9ACj-GTjTpN/6fe20320-9996-11ea-8e87-542bde5ee826 (arbiter)
Brick61: host-f010907c896d:/sf/data/vs/local/FMmFhl-Ouch-V0GX-KXVk-Si1Q-Y4tP-Ep5kZZ/bb36c94c-d284-11ea-9948-f010907c8970
Brick61 VG: FMmFhl-Ouch-V0GX-KXVk-Si1Q-Y4tP-Ep5kZZ
Brick62: host-f010907c8889:/sf/data/vs/local/WT02kL-ubc0-owB1-vtdK-JXR4-4pfJ-BEUMj1/d183b47a-73e0-11ee-aa78-fefcfe35712a
Brick62 VG: WT02kL-ubc0-owB1-vtdK-JXR4-4pfJ-BEUMj1
Brick63: host-905d7c6aaec5:/sf/data/vs/local/hDflfc-oe5j-ieoh-ONv9-Ncdh-luVd-OxjDL9/ecda584c-d284-11ea-9c38-78aa8234a6d7 (arbiter)
Brick64: host-f010907c896d:/sf/data/vs/local/ECExDR-a9o1-sV7y-JhKp-p5fh-RbKF-HV7suT/ba3d7748-d284-11ea-9948-f010907c8970
Brick64 VG: ECExDR-a9o1-sV7y-JhKp-p5fh-RbKF-HV7suT
Brick65: host-f010907c8889:/sf/data/vs/local/s8Z0rt-C1Ef-Id5c-1BRO-w6ey-tv6O-XhB3sI/ba435622-d284-11ea-ba2c-f010907c888c
Brick65 VG: s8Z0rt-C1Ef-Id5c-1BRO-w6ey-tv6O-XhB3sI
Brick66: host-905d7c6aaec5:/sf/data/vs/local/sc0sVv-SxkR-dubc-3W7L-1fhf-6Bcb-vxlLCX/ecdbf558-d284-11ea-9c38-78aa8234a6d7 (arbiter)
Brick67: host-f010907c896d:/sf/data/vs/local/BAoq24-0Th2-3b0D-5GQz-Xotn-8fpi-VBYh19/ba3f1c74-d284-11ea-9948-f010907c8970
Brick67 VG: BAoq24-0Th2-3b0D-5GQz-Xotn-8fpi-VBYh19
Brick68: host-f010907c8889:/sf/data/vs/local/iQ6YCz-TaZu-0zY0-OJ4V-mTXP-A0Vt-d7UZxl/ba2ee886-d284-11ea-ba2c-f010907c888c
Brick68 VG: iQ6YCz-TaZu-0zY0-OJ4V-mTXP-A0Vt-d7UZxl
Brick69: host-78aa8234a779:/sf/data/vs/local/dXRr8R-Z1DI-xZFR-Pleo-00TC-ydEr-FkAaYK/3eece70e-2cb1-11f0-bfd7-fefcfe17ee2a (arbiter)
Brick70: host-f010907c896d:/sf/data/vs/local/0lTaID-BoIr-PPhc-FTow-8WnG-1xLb-Sdy6tF/ba3d6b54-d284-11ea-9948-f010907c8970
Brick70 VG: 0lTaID-BoIr-PPhc-FTow-8WnG-1xLb-Sdy6tF
Brick71: host-f010907c8889:/sf/data/vs/local/8jgn4A-3CcD-0Efw-qStd-hZeD-OCeA-HupcsQ/ba350ee6-d284-11ea-ba2c-f010907c888c
Brick71 VG: 8jgn4A-3CcD-0Efw-qStd-hZeD-OCeA-HupcsQ
Brick72: host-78aa8236cac0:/sf/data/vs/local/8Hti4K-CI77-s5Tp-6jdR-kpED-qcns-gKLYdp/ecde59ec-d284-11ea-9c38-78aa8234a6d7 (arbiter)
Options Reconfigured:
storage.bd-statvfs-reserve-size-per: 10
debug.vst-valid-enable: on
performance.write-behind: off
cluster.vs-layout-by-dir: on
bd.shard: on
bd.shard-size: 16GB
diagnostics.count-fop-hits: on
diagnostics.latency-measurement: on
```
# gluster peer status
```
$ gluster peer status
Number of Peers: 11

Hostname: host-78aa8234addf
Uuid: 16ff66ed-73b3-4af9-8e58-7f052100cf46
State: Peer in Cluster (Connected)

Hostname: host-905d7c6aaec5
Uuid: 7e9bd99a-04b4-46ef-b1e6-fa3482c2cc4b
State: Peer in Cluster (Connected)

Hostname: host-78aa8234b10f
Uuid: 1af7e587-73b1-4bb9-b7c2-ed247e328910
State: Peer in Cluster (Connected)

Hostname: host-542bde60e6aa
Uuid: ea829769-cf35-446b-b972-3b5e9935b786
State: Peer in Cluster (Connected)

Hostname: host-78aa8236cacc
Uuid: 78041c9f-0df2-4fea-b18b-5c4fa6da54e6
State: Peer in Cluster (Connected)

Hostname: host-78aa8234a779
Uuid: f113916c-2712-4293-b033-3d9fc7067300
State: Peer in Cluster (Connected)

Hostname: host-78aa8234a6e3
Uuid: 0beb5ffd-eb69-4dd3-90a3-c2ed790d3996
State: Peer in Cluster (Connected)

Hostname: host-78aa8234a6d7
Uuid: fc53dadc-59f2-4bde-83f9-ced9ca8f69d2
State: Peer in Cluster (Connected)

Hostname: host-542bde5ee826
Uuid: 794e1c9e-cde6-4487-ac93-583415617920
State: Peer in Cluster (Connected)

Hostname: host-78aa8236cac0
Uuid: 418a593a-da2a-4842-ab5c-4ccfc16e9138
State: Peer in Cluster (Connected)

Hostname: host-f010907c896d
Uuid: 00791c53-2663-490e-9b04-9bf76fcdaabf
State: Peer in Cluster (Connected)
```
# gluster pool list
```
$ gluster pool list
UUID					Hostname	State
a88214fe-0c7e-4f8e-bc59-34170eae8255	host-f010907c8889	Connected 
418a593a-da2a-4842-ab5c-4ccfc16e9138	host-78aa8236cac0	Connected 
78041c9f-0df2-4fea-b18b-5c4fa6da54e6	host-78aa8236cacc	Connected 
fc53dadc-59f2-4bde-83f9-ced9ca8f69d2	host-78aa8234a6d7	Connected 
0beb5ffd-eb69-4dd3-90a3-c2ed790d3996	host-78aa8234a6e3	Connected 
00791c53-2663-490e-9b04-9bf76fcdaabf	host-f010907c896d	Connected 
ea829769-cf35-446b-b972-3b5e9935b786	host-542bde60e6aa	Connected 
794e1c9e-cde6-4487-ac93-583415617920	host-542bde5ee826	Connected 
1af7e587-73b1-4bb9-b7c2-ed247e328910	host-78aa8234b10f	Connected 
16ff66ed-73b3-4af9-8e58-7f052100cf46	host-78aa8234addf	Connected 
7e9bd99a-04b4-46ef-b1e6-fa3482c2cc4b	host-905d7c6aaec5	Connected 
f113916c-2712-4293-b033-3d9fc7067300	localhost	Connected 
```
# gluster volume status
```
$ gluster volume status
Status of volume: 8b8974a3_vs_vol_rep2
Gluster process						Port	Online	Pid
------------------------------------------------------------------------------
Brick host-905d7c6aaec5:/sf/data/vs/local/sc0sVv-SxkR-d
ubc-3W7L-1fhf-6Bcb-vxlLCX/4c87f7b8-9996-11ea-bf37-905d7
c6aaec5							49159	Y	110178
Brick host-78aa8236cac0:/sf/data/vs/local/aF0Xjg-ATFL-2
Nq1-PZH8-CtBw-vczF-l7ADzj/4c7722c6-9996-11ea-9adc-78aa8
236cac0							49152	Y	107388
Brick host-542bde5ee826:/sf/data/vs/local/LJJnEf-UnoA-M
aQg-hRPp-nWXq-GYH1-bDa2I6/6fda8898-9996-11ea-8e87-542bd
e5ee826							49161	Y	109135
Brick host-905d7c6aaec5:/sf/data/vs/local/hDflfc-oe5j-i
eoh-ONv9-Ncdh-luVd-OxjDL9/4c82a0b0-9996-11ea-bf37-905d7
c6aaec5							49160	Y	110180
Brick host-78aa8236cac0:/sf/data/vs/local/Ld0cs5-Sguc-Z
v9s-g10b-z2ic-VJVS-lXxetp/4c7852d6-9996-11ea-9adc-78aa8
236cac0							49153	Y	107389
Brick host-542bde5ee826:/sf/data/vs/local/PeKbPj-zwqJ-Y
Qfx-baVO-8as8-l7Ge-VOMXLp/6fdb4922-9996-11ea-8e87-542bd
e5ee826							49162	Y	109132
Brick host-905d7c6aaec5:/sf/data/vs/local/0PlypZ-jfs4-U
x3y-Tacq-bnVc-ctNK-DFpo6d/859df98a-2cb2-11f0-96f0-1c34d
a5c1199							49166	Y	251495
Brick host-78aa8236cac0:/sf/data/vs/local/8Hti4K-CI77-s
5Tp-6jdR-kpED-qcns-gKLYdp/4c98022a-9996-11ea-9adc-78aa8
236cac0							49154	Y	107384
Brick host-542bde5ee826:/sf/data/vs/local/HkxM1t-cxHe-B
5pm-qZ3t-kffm-A9Ng-GMAVkS/6fdbe5a8-9996-11ea-8e87-542bd
e5ee826							49163	Y	109144
Brick host-905d7c6aaec5:/sf/data/vs/local/5xSUYh-Knup-V
stB-XC1l-0aML-poQ4-FbUaLX/4c7e1630-9996-11ea-bf37-905d7
c6aaec5							49162	Y	110164
Brick host-78aa8236cac0:/sf/data/vs/local/53T0AS-7X3s-h
Zfz-OehZ-Bpje-ojfc-kK315X/4c772b5e-9996-11ea-9adc-78aa8
236cac0							49155	Y	107385
Brick host-542bde5ee826:/sf/data/vs/local/re7Afn-dea8-C
GT9-fU1Q-6uUP-RLu2-2eoZ94/6fdc7c5c-9996-11ea-8e87-542bd
e5ee826							49164	Y	109211
Brick host-78aa8234addf:/sf/data/vs/local/iyqv69-x4RL-w
bvk-bux2-FaDn-BX8g-mne762/4d3cd93a-9996-11ea-acb6-78aa8
234addf							N/A	Y	105922
Brick host-78aa8234a6e3:/sf/data/vs/local/MjfY1T-e4yD-1
n1e-jSl0-i1fg-BHMh-T846j6/4c6b7fd4-9996-11ea-b7f2-78aa8
234a6e3							49152	Y	98160
Brick host-78aa8234a6d7:/sf/data/vs/local/mQLhg8-lXKk-A
WOR-zfHA-FdiJ-ajAm-KJWn3V/6fdd14aa-9996-11ea-8e87-542bd
e5ee826							49160	Y	104558
Brick host-78aa8234addf:/sf/data/vs/local/i1yYLt-j2jC-U
yx3-J30P-gkQ1-OEFI-RUzjnj/4c80d3ca-9996-11ea-acb6-78aa8
234addf							N/A	Y	105932
Brick host-78aa8234a6e3:/sf/data/vs/local/K1T56F-6teW-y
WJa-odXo-Xciz-p5VE-RhHGfZ/4c7c6916-9996-11ea-b7f2-78aa8
234a6e3							49153	Y	98156
Brick host-78aa8234a6d7:/sf/data/vs/local/UVnv6J-fOmK-q
etj-pPQM-pfnK-DqJw-QMrlri/6fdd7440-9996-11ea-8e87-542bd
e5ee826							49165	Y	104557
Brick host-78aa8234addf:/sf/data/vs/local/bIeBZ2-0VO4-8
6dB-Sci1-cl1q-X32G-sFjRcj/4c79f9ce-9996-11ea-acb6-78aa8
234addf							N/A	Y	105960
Brick host-78aa8234a6e3:/sf/data/vs/local/3BM2x6-WygF-c
Xn6-ZbS9-WeT5-ZLOH-tDHSP1/4c6b426c-9996-11ea-b7f2-78aa8
234a6e3							49154	Y	98154
Brick host-78aa8234a6d7:/sf/data/vs/local/9KBFqX-kjuo-L
ihM-GGrP-jGFh-qcpV-4m8mBQ/6fddd0de-9996-11ea-8e87-542bd
e5ee826							49166	Y	104599
Brick host-78aa8234addf:/sf/data/vs/local/GkDmju-Xej7-K
7Qf-iRQf-SUy1-ZgVf-WzFglz/4c6e0182-9996-11ea-acb6-78aa8
234addf							N/A	Y	105928
Brick host-78aa8234a6e3:/sf/data/vs/local/2Xo0M8-ovB3-A
1GO-5qKe-omU8-RHDA-vNrA1A/58569aea-9996-11ea-b7f2-78aa8
234a6e3							49155	Y	98259
Brick host-78aa8234a6d7:/sf/data/vs/local/4cHibt-ZJv0-P
bOw-xKfj-R7yS-9bAG-DNK0Ic/6fde2282-9996-11ea-8e87-542bd
e5ee826							49167	Y	104594
Brick host-542bde60e6aa:/sf/data/vs/local/yhhsaQ-YJgV-w
sn7-iVBr-Krp1-aYs4-5XQYeR/4c76aa6c-9996-11ea-8172-542bd
e60e6aa							49160	Y	100094
Brick host-78aa8236cacc:/sf/data/vs/local/yBIQUc-mfGU-c
Ocs-ZopN-zbyp-3KC7-ZAh25J/4c7aebae-9996-11ea-b2a5-78aa8
236cacc							49152	Y	108321
Brick host-78aa8234a6e3:/sf/data/vs/local/2Xo0M8-ovB3-A
1GO-5qKe-omU8-RHDA-vNrA1A/6fde7570-9996-11ea-8e87-542bd
e5ee826							49156	Y	97722
Brick host-542bde60e6aa:/sf/data/vs/local/7xopQw-188v-j
Hhf-vxtG-M84W-fe2p-mq4237/4c76a60c-9996-11ea-8172-542bd
e60e6aa							49161	Y	100117
Brick host-78aa8236cacc:/sf/data/vs/local/ikDMga-jQRN-8
nYG-Tbfv-DAbp-Wfg9-h6IUQp/4c77cbae-9996-11ea-b2a5-78aa8
236cacc							49153	Y	108328
Brick host-78aa8234a6e3:/sf/data/vs/local/3BM2x6-WygF-c
Xn6-ZbS9-WeT5-ZLOH-tDHSP1/6fdec7b4-9996-11ea-8e87-542bd
e5ee826							49157	Y	97697
Brick host-542bde60e6aa:/sf/data/vs/local/26kVS0-AVlZ-p
I0r-yCOp-uTO4-7NdL-r712xW/4c76a788-9996-11ea-8172-542bd
e60e6aa							49162	Y	100093
Brick host-78aa8236cacc:/sf/data/vs/local/LWvAYT-Pyia-K
4yA-uQSp-sqgB-lDF9-JNcfhM/4c7fe2c6-9996-11ea-b2a5-78aa8
236cacc							49154	Y	108335
Brick host-78aa8234a6e3:/sf/data/vs/local/MjfY1T-e4yD-1
n1e-jSl0-i1fg-BHMh-T846j6/6fdf1b1a-9996-11ea-8e87-542bd
e5ee826							49158	Y	97751
Brick host-542bde60e6aa:/sf/data/vs/local/09U8Cc-kcbZ-E
qPm-ppKf-JSPk-3NZb-EwPitz/4c76a6de-9996-11ea-8172-542bd
e60e6aa							49163	Y	100230
Brick host-78aa8236cacc:/sf/data/vs/local/4HOOS1-kGc5-R
KIb-4NEB-wEeQ-9ACj-GTjTpN/4c77cf1e-9996-11ea-b2a5-78aa8
236cacc							49155	Y	108418
Brick host-78aa8234a6e3:/sf/data/vs/local/K1T56F-6teW-y
WJa-odXo-Xciz-p5VE-RhHGfZ/6fdf740c-9996-11ea-8e87-542bd
e5ee826							49159	Y	97711
Brick host-78aa8234b10f:/sf/data/vs/local/zNix3T-gy3c-n
L7g-0UX8-jT3U-8DVX-N1wX1r/4c960240-9996-11ea-8a2a-78aa8
234b10f							49156	Y	107816
Brick host-78aa8234a779:/sf/data/vs/local/vpSgWv-k1bK-6
PH3-9Q0P-ojG7-11M7-R0aVjy/5b680b42-9996-11ea-8da3-78aa8
234a779							49180	Y	103704
Brick host-542bde60e6aa:/sf/data/vs/local/09U8Cc-kcbZ-E
qPm-ppKf-JSPk-3NZb-EwPitz/6fdfc4a2-9996-11ea-8e87-542bd
e5ee826							49164	Y	99659
Brick host-78aa8234b10f:/sf/data/vs/local/spqkAb-hEwy-D
eqA-wGhW-7RIr-ft6L-PRVIyi/4c950250-9996-11ea-8a2a-78aa8
234b10f							49157	Y	107801
Brick host-78aa8234a779:/sf/data/vs/local/rIAfjL-wj1r-l
C31-zh0H-Pgli-5plg-0TajiR/4c7b7cfe-9996-11ea-8da3-78aa8
234a779							49181	Y	103734
Brick host-542bde60e6aa:/sf/data/vs/local/yhhsaQ-YJgV-w
sn7-iVBr-Krp1-aYs4-5XQYeR/6fe0175e-9996-11ea-8e87-542bd
e5ee826							49165	Y	99656
Brick host-78aa8234b10f:/sf/data/vs/local/WEfzqv-Oy2a-d
zd4-MvKA-VVdD-p03w-4nEKa0/4c94fb02-9996-11ea-8a2a-78aa8
234b10f							49158	Y	107893
Brick host-78aa8234a779:/sf/data/vs/local/dXRr8R-Z1DI-x
ZFR-Pleo-00TC-ydEr-FkAaYK/4c8e9b04-9996-11ea-8da3-78aa8
234a779							49182	Y	103930
Brick host-542bde60e6aa:/sf/data/vs/local/7xopQw-188v-j
Hhf-vxtG-M84W-fe2p-mq4237/6fe069fc-9996-11ea-8e87-542bd
e5ee826							49166	Y	99664
Brick host-78aa8234b10f:/sf/data/vs/local/DAc32A-12S1-n
qcU-3V02-prx2-1suw-hxgOiy/4ca1bcc0-9996-11ea-8a2a-78aa8
234b10f							49159	Y	107794
Brick host-78aa8234a779:/sf/data/vs/local/ZD939o-rxdw-F
IqF-ObaN-XoLd-Ibb2-Xg1fLT/4c7bfe86-9996-11ea-8da3-78aa8
234a779							49183	Y	103929
Brick host-542bde60e6aa:/sf/data/vs/local/26kVS0-AVlZ-p
I0r-yCOp-uTO4-7NdL-r712xW/6fe0baa6-9996-11ea-8e87-542bd
e5ee826							49167	Y	99671
Brick host-78aa8234a6d7:/sf/data/vs/local/mQLhg8-lXKk-A
WOR-zfHA-FdiJ-ajAm-KJWn3V/4c825628-9996-11ea-98c0-78aa8
234a6d7							49161	Y	105738
Brick host-542bde5ee826:/sf/data/vs/local/re7Afn-dea8-C
GT9-fU1Q-6uUP-RLu2-2eoZ94/4c7c1f7e-9996-11ea-8e87-542bd
e5ee826							49160	Y	109647
Brick host-78aa8236cacc:/sf/data/vs/local/LWvAYT-Pyia-K
4yA-uQSp-sqgB-lDF9-JNcfhM/6fe11578-9996-11ea-8e87-542bd
e5ee826							49156	Y	107868
Brick host-78aa8234a6d7:/sf/data/vs/local/UVnv6J-fOmK-q
etj-pPQM-pfnK-DqJw-QMrlri/4c81cee2-9996-11ea-98c0-78aa8
234a6d7							49162	Y	105614
Brick host-542bde5ee826:/sf/data/vs/local/PeKbPj-zwqJ-Y
Qfx-baVO-8as8-l7Ge-VOMXLp/4c7a942e-9996-11ea-8e87-542bd
e5ee826							49165	Y	109823
Brick host-78aa8236cacc:/sf/data/vs/local/ikDMga-jQRN-8
nYG-Tbfv-DAbp-Wfg9-h6IUQp/6fe165aa-9996-11ea-8e87-542bd
e5ee826							49157	Y	107895
Brick host-78aa8234a6d7:/sf/data/vs/local/9KBFqX-kjuo-L
ihM-GGrP-jGFh-qcpV-4m8mBQ/4d404f70-9996-11ea-98c0-78aa8
234a6d7							49163	Y	105617
Brick host-542bde5ee826:/sf/data/vs/local/LJJnEf-UnoA-M
aQg-hRPp-nWXq-GYH1-bDa2I6/4c958e50-9996-11ea-8e87-542bd
e5ee826							49166	Y	109698
Brick host-78aa8236cacc:/sf/data/vs/local/yBIQUc-mfGU-c
Ocs-ZopN-zbyp-3KC7-ZAh25J/6fe1b4ba-9996-11ea-8e87-542bd
e5ee826							49158	Y	108115
Brick host-78aa8234a6d7:/sf/data/vs/local/4cHibt-ZJv0-P
bOw-xKfj-R7yS-9bAG-DNK0Ic/4c8156e2-9996-11ea-98c0-78aa8
234a6d7							49164	Y	105620
Brick host-542bde5ee826:/sf/data/vs/local/HkxM1t-cxHe-B
5pm-qZ3t-kffm-A9Ng-GMAVkS/4c7aff54-9996-11ea-8e87-542bd
e5ee826							49167	Y	109699
Brick host-78aa8236cacc:/sf/data/vs/local/4HOOS1-kGc5-R
KIb-4NEB-wEeQ-9ACj-GTjTpN/6fe20320-9996-11ea-8e87-542bd
e5ee826							49159	Y	107950
Brick host-f010907c896d:/sf/data/vs/local/FMmFhl-Ouch-V
0GX-KXVk-Si1Q-Y4tP-Ep5kZZ/bb36c94c-d284-11ea-9948-f0109
07c8970							49152	Y	98957
Brick host-f010907c8889:/sf/data/vs/local/WT02kL-ubc0-o
wB1-vtdK-JXR4-4pfJ-BEUMj1/d183b47a-73e0-11ee-aa78-fefcf
e35712a							49157	Y	201535
Brick host-905d7c6aaec5:/sf/data/vs/local/hDflfc-oe5j-i
eoh-ONv9-Ncdh-luVd-OxjDL9/ecda584c-d284-11ea-9c38-78aa8
234a6d7							49163	Y	109781
Brick host-f010907c896d:/sf/data/vs/local/ECExDR-a9o1-s
V7y-JhKp-p5fh-RbKF-HV7suT/ba3d7748-d284-11ea-9948-f0109
07c8970							49153	Y	99096
Brick host-f010907c8889:/sf/data/vs/local/s8Z0rt-C1Ef-I
d5c-1BRO-w6ey-tv6O-XhB3sI/ba435622-d284-11ea-ba2c-f0109
07c888c							49153	Y	131188
Brick host-905d7c6aaec5:/sf/data/vs/local/sc0sVv-SxkR-d
ubc-3W7L-1fhf-6Bcb-vxlLCX/ecdbf558-d284-11ea-9c38-78aa8
234a6d7							49164	Y	109792
Brick host-f010907c896d:/sf/data/vs/local/BAoq24-0Th2-3
b0D-5GQz-Xotn-8fpi-VBYh19/ba3f1c74-d284-11ea-9948-f0109
07c8970							49154	Y	99270
Brick host-f010907c8889:/sf/data/vs/local/iQ6YCz-TaZu-0
zY0-OJ4V-mTXP-A0Vt-d7UZxl/ba2ee886-d284-11ea-ba2c-f0109
07c888c							49154	Y	75367
Brick host-78aa8234a779:/sf/data/vs/local/dXRr8R-Z1DI-x
ZFR-Pleo-00TC-ydEr-FkAaYK/3eece70e-2cb1-11f0-bfd7-fefcf
e17ee2a							49184	Y	49098
Brick host-f010907c896d:/sf/data/vs/local/0lTaID-BoIr-P
Phc-FTow-8WnG-1xLb-Sdy6tF/ba3d6b54-d284-11ea-9948-f0109
07c8970							49155	Y	99381
Brick host-f010907c8889:/sf/data/vs/local/8jgn4A-3CcD-0
Efw-qStd-hZeD-OCeA-HupcsQ/ba350ee6-d284-11ea-ba2c-f0109
07c888c							49155	Y	2025
Brick host-78aa8236cac0:/sf/data/vs/local/8Hti4K-CI77-s
5Tp-6jdR-kpED-qcns-gKLYdp/ecde59ec-d284-11ea-9c38-78aa8
234a6d7							49156	Y	107130
NFS Server on localhost					2049	Y	103327
Self-heal Daemon on localhost				N/A	Y	119332
NFS Server on host-78aa8234addf				2049	Y	105513
Self-heal Daemon on host-78aa8234addf			N/A	Y	60406
NFS Server on host-f010907c896d				2049	Y	97734
Self-heal Daemon on host-f010907c896d			N/A	Y	52412
NFS Server on host-78aa8236cac0				2049	Y	106890
Self-heal Daemon on host-78aa8236cac0			N/A	Y	67594
NFS Server on host-78aa8236cacc				2049	Y	107571
Self-heal Daemon on host-78aa8236cacc			N/A	Y	81480
NFS Server on host-542bde60e6aa				2049	Y	100779
Self-heal Daemon on host-542bde60e6aa			N/A	Y	55726
NFS Server on host-f010907c8889				2049	Y	154532
Self-heal Daemon on host-f010907c8889			N/A	Y	88155
NFS Server on host-905d7c6aaec5				2049	Y	109415
Self-heal Daemon on host-905d7c6aaec5			N/A	Y	90456
NFS Server on host-78aa8234b10f				2049	Y	107332
Self-heal Daemon on host-78aa8234b10f			N/A	Y	18730
NFS Server on host-78aa8234a6e3				2049	Y	97384
Self-heal Daemon on host-78aa8234a6e3			N/A	Y	255896
NFS Server on host-542bde5ee826				2049	Y	108865
Self-heal Daemon on host-542bde5ee826			N/A	Y	101977
NFS Server on host-78aa8234a6d7				2049	Y	104206
Self-heal Daemon on host-78aa8234a6d7			N/A	Y	249913
 
Task Status of Volume 8b8974a3_vs_vol_rep2
------------------------------------------------------------------------------
Task                 : Rebalance           
ID                   : 26a05664-a4f2-4a78-810e-e1190e543d04
Status               : not started         
```
# 
```
$ gluster volume list
8b8974a3_vs_vol_rep2
```
