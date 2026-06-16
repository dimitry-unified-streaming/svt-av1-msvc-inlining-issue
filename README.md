README for SVT-AV1 MSVC inlining issue test case
================================================

This is a minimized version of the problematic code from the SVT-AV1 project, as
mentioned in https://gitlab.com/AOMediaCodec/SVT-AV1/-/work_items/2322
("Windows: broken output with Visual Studio 2026 release builds").

The issue seems to stem from the optimizer inlining the `yy_unpacklo_epi128`
function incorrectly in the `transpose_16bit_16x16_avx2` function. This shows up
in `main` when the `svt_pack_and_transpose_16x16` function returns a "known-bad"
result, aka `dqcoeff_16_bad`.

If the compiler is told to not inline the `yy_unpacklo_epi128` function, by
defining `ENABLE_WORKAROUND` (for example via `/DENABLE_WORKAROUND` on the
command line), the `svt_pack_and_transpose_16x16` function returns the correct
result, aka `dqcoeff_16_good`.

NOTE: running this example requires a machine capable of running AVX2
instructions!

To build the test case:

```
cl /arch:AVX2 /O2 svt-av1-msvc-inline-issue.c
```

Followed by running the resulting `svt-av1-msvc-inline-issue.exe`, should show:

```
svt_pack_and_transpose_16x16 gives a known-bad result
```

Enable the noinline workaround with:

```
cl /arch:AVX2 /O2 /DENABLE_WORKAROUND svt-av1-msvc-inline-issue.c
```

Running the resulting `svt-av1-msvc-inline-issue.exe`, should then show:

```
svt_pack_and_transpose_16x16 works correctly
```
