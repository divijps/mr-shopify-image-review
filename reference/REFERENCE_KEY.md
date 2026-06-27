# Reference Key — Mr/Mrs Shirt SKU classification

Validated against the original renders on 2026-06-23. This is the ground truth
used to classify every render and lifestyle image.

## SKU structure
`MR - <sleeve> - <shirtColor> - <detailVariant>`

| Field | Codes |
|-------|-------|
| sleeve | `LSD` = long sleeve · `SSD` = short sleeve |
| shirtColor | `BLK` = black · `WHT` = white |
| detailVariant | 8 values, see below |

32 folders = 2 sleeves × 2 colors × 8 detail variants.

## The 8 detail variants — decision tree
Classify by **button appearance** first, then **crest placement**:

```
Buttons matte BLACK (same as shirt), black-on-black crest ........ BLKBLK
Buttons TWO-TONE (polished silver rim + gold engraved center) .... MIXGLD
Buttons GOLD:
    crest on COLLAR, none on chest ............................... GLDCL
    crest on CHEST + ENGRAVED-face buttons ....................... GLDCH
    crest on CHEST + PLAIN smooth dome buttons ................... GLDMR
Buttons SILVER:
    crest on COLLAR, none on chest ............................... SLVCL
    crest on CHEST + ENGRAVED-face buttons ....................... SLVCH
    crest on CHEST + PLAIN smooth dome buttons ................... SLVMR
```

### Key discriminators
- **MIXGLD** is the trap: two-tone (silver ring, gold middle), *lighter* than solid
  gold. NOT plain silver, NOT plain gold. No chest crest (crest is on back collar).
- **CH vs MR** = button face only. CH = ornate engraved crest on the button.
  MR = plain smooth metal dome. **Same color, same chest crest.** If the buttons
  aren't clearly visible (e.g. a distant lifestyle shot), the image is
  CH/MR-ambiguous → tag `detail_ambiguous` and surface for review.
- **CL** = no chest crest at all (crest sits on the collar). Easy to tell from
  CH/MR because the chest is plain.
- See montages: `01_button_compare.jpg`, `02_variants_front.jpg`, `03_chest_crest.jpg`.

## View-type recognition (for ordering inside each folder)
| view | looks like |
|------|-----------|
| `front` | full shirt, front facing, flat/ghost-mannequin, buttons + chest visible |
| `back` | full shirt from behind, yoke + back collar, no placket |
| `cuff_detail` | cropped close-up of sleeve cuff + buttons |
| `collar_detail` | cropped close-up of collar / chest crest (often shows "Mrs." inner label) |
| `backcollar_detail` | cropped close-up of the back neck / collar crest |
| `lifestyle` | a person wearing the shirt, real-photo look (IMG_* files) |

## Target output order per folder
1 front · 2 lifestyle · 3 back · 4 detail · 5–6 fill (extra render or lifestyle).
Files named `01-<SKU>.jpg` … `06-<SKU>.jpg`. JPEG, 2048px long side, q85.

## Notes
- Inner collar label reads **"Mrs."** on these renders even though the folder
  prefix is `MR`. SKU codes are kept verbatim; nothing is renamed.
- A lifestyle shot whose sleeves are cropped out (can't tell long vs short) but
  whose color + detail are clear → reusable across BOTH the LSD and SSD folder of
  that color+detail; tag `sleeve_unknown`.
