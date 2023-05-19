# Updating BCD using the results

In this repository, the `update-bcd` script can be used to update the existing BCD entries. This script assumes you have the following:

- A local checkout of:
  - [This repository](https://github.com/GooborgStudios/mdn-bcd-collector)
  - [mdn/browser-compat-data](https://github.com/mdn/browser-compat-data) at `../browser-compat-data` (or the path set as the `BCD_DIR` environment variable)
  - [mdn-bcd-results](https://github.com/GooborgStudios/mdn-bcd-results), preferably at `../mdn-bcd-results`

To update BCD, run the following command:

```sh
npm run update-bcd
```

This will update BCD using all of the results files in the `../mdn-bcd-results` folder. To use results in a different path, and/or to use a specific file, you may specify any number of paths as arguments:

```sh
npm run update-bcd ../local-results
npm run update-bcd ../mdn-bcd-results/9.1.0-chrome-112.0.0.0-mac-os-10.15.7-79d130f929.json
```

## Limit changes by BCD path

To limit changes to a specific BCD path, such as by category or a specific interface, you may use the `-p/--path` argument.

Updating a specific category:

```sh
npm run update-bcd -- --p=css.properties
npm run update-bcd -- -p css.properties
```

Updating a specific entry, ex. the `appendChild()` method on `Node`:

```sh
npm run update-bcd -- --path=api.Node.appendChild
npm run update-bcd -- -p api.Node.appendChild
```

Updating a specific feature and its children, ex. the `Document` API (also updates `api.Document.*`, ex. `api.Document.body`):

```sh
npm run update-bcd -- --path=api.Document
npm run update-bcd -- -p api.Document
```

Updating paths matched with wildcards, ex. everything related to WebRTC:

```sh
npm run update-bcd -- --path=api.RTC*
npm run update-bcd -- -p api.RTC*
```

Note: `update-bcd` used to take a `-c/--category` parameter. This has been deprecated in favor of the more versatile `-p/--path`.

## Limit changes to non-ranged only

The `-e/--exact-only` argument can be used to only update BCD when we have an exact version number and skip any ranges:

```sh
npm run update-bcd -- --exact-only
npm run update-bcd -- -e
```

## Limit changes by borwser

The `-b/--browser` argument can be used to only update data for one or more browsers:

```sh
npm run update-bcd -- --browser=safari --browser=safari_ios
npm run update-bcd -- -b safari -b safari_ios
```

The `-r/--release` argument can be used to only update data for a specific browser release, ex. Firefox 84:

```sh
npm run update-bcd -- --browser=firefox --release=84
npm run update-bcd -- -b firefox -r 84
```

This will only make changes that set either `version_added` or `version_removed` to "84".

## Custom ranged version format

When the results don't have enough data to determine an exact version, ranges which aren't valid in BCD may be added:

- "≤N" for any release, not just the ranged versions allowed by BCD.
- "M> ≤N" when a feature is _not_ in M and _is_ in N, but there are releases between the two for which support is unknown.

In both cases, the uncertainty has to be resolved by hand before submitting the data to BCD.

## New releases or browsers

If you have results from a browser not yet in BCD, first add the release in `../browser-compat-data/browsers/`. This is because the full version (from the `User-Agent` header) is mapped to BCD browser release as part of the processing.