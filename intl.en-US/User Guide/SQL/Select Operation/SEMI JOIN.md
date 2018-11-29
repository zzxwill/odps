# SEMI JOIN {#concept_lls_skb_wdb .concept}

MaxCompute supports SEMI JOIN.  In SEMI JOIN, the right table does not appear in the result set and is only used to filter data in the left table.  Supported syntaxes include:  LEFT SEMI JOIN and LEFT ANTI JOIN.

## LEFT SEMI JOIN {#section_kjz_pwb_wdb .section}

When a JOIN condition is valid, data in the left table is returned.  That is, if the ID of a row in mytable1 appears in all IDs in mytable2, this row is saved in the result set.

**For example**:

```
SELECT * from mytable1 a LEFT SEMI JOIN mytable2 b on a.id=b.id;
```

Only the data in mytable1 is returned if the ID of mytable1 appears in the ID of mytable2.

## LEFT ANTI JOIN {#section_c1y_twb_wdb .section}

When a JOIN condition is invalid, data in the left table is returned.  That is, if the ID of a row in mytable1 does not appear in any ID in mytable2, this row is stored in the result set.

**For example**:

```
SELECT * from mytable1 a LEFT ANTI JOIN mytable2 b on a.id=b.id;
```

Only the data in mytable1 is returned if the ID of mytable1 does not appear in the ID of mytable2.

