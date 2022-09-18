# Modified Hands On keyboard layout (hereby christened WHaLeDB)

Seems to fit my personal preferences and most often typed words quite well, inspired by Alan Reiser's comfortable Hands On keyboard layout.
An important goal was to make SQL statements feel good to type for me on a standard staggered ANSI board while keeping QWERTY shortcuts. I am sure it is not awesome for extremely low SFB counts but I do not much care.

Feeding `https://kla.keyboard-design.com` with my oft-typed words showed good results FWIW.
The analyzer seems to consider it to be about on par with MTGAP and a bit better than colemak (DH) and Dvorak. For me it just feels more comfortable.
Sample text:

```
pg_ctl start
select, insert, update, delete, from, while, when, where, in array, integer, varchar, order by, group by, limit, partition, loop, upsert, rollup, char, commit, sql, lock, create, drop, trigger, procedure, function.;;;
rollback
ls /var/lib/pgsql/data
select usename, count (usename) from pg_stat_activity where usename is not null group by 1 order by 2;
git pull
git push
select
current_database(),
schemaname,
tablename,
/*reltuples::bigint, relpages::bigint, otta,*/
round((
case when otta = 0 then
0.0
else
sml.relpages::float / otta
end)::numeric, 1) as tbloat,
case when relpages < otta then
0
else
bs * (sml.relpages - otta)::bigint
end as wastedbytes,
iname,
/*ituples::bigint, ipages::bigint, iotta,*/
round((
case when iotta = 0
or ipages = 0 then
0.0
else
ipages::float / iotta
end)::numeric, 1) as ibloat,
case when ipages < iotta then
0
else
bs * (ipages - iotta)
end as wastedibytes
from (
select
schemaname,
tablename,
cc.reltuples,
cc.relpages,
bs,
ceil((cc.reltuples * ((datahdr + ma - (
case when datahdr % ma = 0 then
ma
else
datahdr % ma
end)) + nullhdr2 + 4)) / (bs - 20::float)) as otta,
coalesce(c2.relname, '?') as iname,
coalesce(c2.reltuples, 0) as ituples,
coalesce(c2.relpages, 0) as ipages,
coalesce(ceil((c2.reltuples * (datahdr - 12)) / (bs - 20::float)), 0) as iotta -- very rough approximation, assumes all cols
from (
select
ma,
bs,
schemaname,
tablename,
(datawidth + (hdr + ma - (
case when hdr % ma = 0 then
ma
else
hdr % ma
end)))::numeric as datahdr,
(maxfracsum * (nullhdr + ma - (
case when nullhdr % ma = 0 then
ma
else
nullhdr % ma
end))) as nullhdr2
from (
select
schemaname,
tablename,
hdr,
ma,
bs,
sum((1 - null_frac) * avg_width) as datawidth,
max(null_frac) as maxfracsum,
hdr + (
select
1 + count(*) / 8
from
pg_stats s2
where
null_frac <> 0
and s2.schemaname = s.schemaname
and s2.tablename = s.tablename) as nullhdr
from
pg_stats s,
(
select
(
select
current_setting('block_size')::numeric) as bs,
case when substring(v, 12, 3) in ('8.0', '8.1', '8.2') then
27
else
23
end as hdr,
case when v ~ 'mingw32' then
8
else
4
end as ma
from (
select
version() as v) as foo) as constants
group by
1,
2,
3,
4,
5) as foo) as rs
join pg_class cc on cc.relname = rs.tablename
join pg_namespace nn on cc.relnamespace = nn.oid
and nn.nspname = rs.schemaname
and nn.nspname <> 'information_schema'
left join pg_index i on indrelid = cc.oid
left join pg_class c2 on c2.oid = i.indexrelid) as sml
order by
wastedbytes desc;
```

```
 whldbykuq' 
  rsntgpaeoi;
    zxcvjfm,./
```

