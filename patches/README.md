# Lumengine patches to OpenUSD

The workflow applies every `*.patch` here, in name order, to the OpenUSD
release it checks out, before building. They are kept here; none is proposed
upstream. A patched build is tagged after the OpenUSD release with a Lumengine
suffix (`v26.11.1-lm1`), and the archives take that name, so they never replace
the archives of an unpatched release.

On an OpenUSD upgrade, check that each patch still applies
(`git apply --check --ignore-whitespace`) on the new tag, and rebase it if not.

## 0001-usdImaging-native-instancing-flattened-providers.patch

Lets a client choose the flattened data source providers native instancing
flattens each prototype with:
`UsdImagingCreateSceneIndicesInfo::nativeInstancingFlattenedDataSourceProviders`,
passed to a new `UsdImagingNiPrototypePropagatingSceneIndex::New` overload.
Null keeps OpenUSD's behaviour (`UsdImagingFlattenedDataSourceProviders()`).

Lumengine resolves the transform and the visibility hierarchy itself, in its
ECS, and must not receive them flattened inside prototypes. Without this
option it had to copy UsdImaging's whole native instancing implementation
(about 4,400 lines) to change one call, and to rebuild UsdImaging's scene index
chain by hand around the copy. With it, Lumengine calls
`UsdImagingCreateSceneIndices` and passes providers without `xform` and
`visibility`.
