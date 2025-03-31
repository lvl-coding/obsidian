社区建议每个 OSD 100 到 200 个 PG
pg计算公式为( Target PGs per OSD ) x ( OSD nums) x ( %Data )/( Size )，结果最小为OSD nums/Size的值，pg个数为最接近结果的2的整数幂，如果幂值比计算结果小25%以上，则选取下一个大的幂值