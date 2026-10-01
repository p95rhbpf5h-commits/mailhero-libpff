# libpff for MailHero

This repository is the exact libpff source that MailHero for macOS builds and
ships. It exists so that MailHero can satisfy the GNU Lesser General Public
License (LGPL) by making the complete corresponding source of the shared
library available to every user.

## Provenance

- Upstream project: libpff by Joachim Metz, part of the libyal suite  
  https://github.com/libyal/libpff
- Version: `20231205` source distribution (`libpff-alpha-20231205.tar.gz`)
- Modifications: **none**. The tree is the unmodified upstream source
  distribution, including the bundled libyal dependency libraries
  (libcerror, libcdata, libbfio, …) that the distribution carries.

## License

libpff is licensed under the GNU Lesser General Public License version 3 or
later. The full text is in `COPYING.LESSER`, and the GNU General Public License
version 3 that it supplements is in `COPYING`.

## How MailHero uses it

MailHero links libpff **dynamically**. The app bundle contains
`Contents/Frameworks/libpff.1.dylib`, built from this tree as a universal
(arm64 + x86_64) macOS dynamic library and loaded by the MailArchiveKit
framework at run time. Because the library is a separate, replaceable file,
you can rebuild it from this source and substitute your own copy as the LGPL
intends.

The build MailHero uses is, per architecture:

```sh
./configure --host=<arch>-apple-darwin --enable-shared --disable-static \
            --disable-python --disable-nls
make
# libpff/.libs/libpff.1.dylib
```

followed by `lipo -create` to merge the arm64 and x86_64 slices and
`install_name_tool -id @rpath/libpff.1.dylib` so the app can locate it. The
exact script lives in the MailHero repository at
`Scripts/build-vendored-libpff.sh`.

## Rebuilding and substituting the library

1. Build `libpff.1.dylib` as above (or with any compatible libpff 20231205
   build).
2. Replace `MailHero.app/Contents/Frameworks/libpff.1.dylib` with your build.
3. Re-sign the app for your own machine, for example
   `codesign --force --deep --sign - MailHero.app`.

macOS requires the replaced bundle to be re-signed; an ad-hoc signature is
sufficient to run the app locally.
