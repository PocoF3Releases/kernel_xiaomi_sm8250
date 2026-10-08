# Xiaomi defconfig generation

Run `scripts/update_defconfig` from any working directory. Its own location identifies the kernel root; the nearest ancestor Android checkout provides the compiler. Do not edit `arch/arm64/configs/*_defconfig` for these devices. Edit `vendor/kona-perf_defconfig`, `vendor/xiaomi/sm8250-common.config` or the device `.config`, then generate.

## Host requirements

Linux or WSL, Bash, GNU make/coreutils, flex, bison, and a working Clang/LLVM toolchain plus host C/C++ headers. Ubuntu packages: `sudo apt-get install build-essential flex bison`. Kconfig generation does not build the full kernel and does not require a separate GCC cross-toolchain. For full kernel builds, additional dependencies may be required.

The default compiler follows `TARGET_KERNEL_CLANG_VERSION` in the common device BoardConfig, then Soong's default, then the newest installed `clang-r*` revision. An explicitly configured revision must exist; the script never silently substitutes another one. `--clang-version latest` tests the newest installed Android revision without changing the ROM's compiler selection. This does not establish full-kernel compatibility with that compiler.

## Examples

```sh
scripts/update_defconfig                       # all twelve devices
scripts/update_defconfig alioth                # one device
scripts/update_defconfig --check               # read-only consistency check
scripts/update_defconfig --check --clang-version latest alioth
scripts/update_defconfig --android-root /path/to/android alioth
scripts/update_defconfig --clang-dir /path/to/clang/bin -j 4 alioth
```

The script uses the same LLVM/integrated-assembler settings for Kconfig and savedefconfig, isolates temporary outputs and cleans them on success/failure. Every selected device must generate successfully before any source config is updated. `--check` exits nonzero on differences without writing source configs. Existing kernel `temp` directories are untouched. Compiler/make errors remain visible and stop generation.

LLVM usage reference: https://docs.kernel.org/kbuild/llvm.html
