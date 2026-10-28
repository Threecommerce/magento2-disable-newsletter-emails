# Changelog

All notable changes to this module are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.4] - 2026-07-09

### Fixed
- **Deploy blocker (developer mode):** `etc/adminhtml/system.xml` no longer validated
  against `system_file.xsd` because a `<resource>` (ACL) element was declared inside
  `<group id="subscription">`. The schema only allows `<resource>` inside `<section>`,
  so `bin/magento setup:upgrade` / `setup:di:compile` aborted in developer mode
  (production mode skips schema validation, which is why it stayed latent). Removed the
  element; the fields inherit the core `newsletter` section ACL
  (`Magento_Newsletter::newsletter`), which is the intended behaviour. Magento does not
  support per-group ACL resources in `system.xml`.

### Changed
- Removed the hardcoded `"version"` field from `composer.json`. Composer/Satis honour
  the explicit `version` over the git tag, so tag `v1.0.3` was being published as `1.0.2`
  and silently dropped as a duplicate by the Satis repository. The version is now derived
  from the git tag, as recommended. (Tag `v1.0.3` was published broken for this reason and
  superseded by `v1.0.4`.)

## [1.0.2] - 2026-06-19

### Fixed
- **Critical:** the plugin read configuration from the wrong path
  (`newletteroptions/email/*`), which never matched the values written by
  `system.xml` or the defaults in `config.xml` (`newsletter/subscription/*`).
  The lookup always returned `null`, so the import-mode flag was set
  unconditionally and **all** newsletter emails were suppressed regardless of
  the admin settings. The path is now `newsletter/subscription/*`, so the
  per-email toggles work as intended.

### Changed
- Use `ScopeConfigInterface::isSetFlag()` instead of `getValue()` for the
  Yes/No toggles, returning a proper boolean.
- Extracted the config path prefix into a class constant.
- Linked the previously orphaned ACL resource
  (`Threecommerce_DisableNewsletterEmail::NewsletterEmailSettings`) to the
  configuration group in `system.xml`, so admin access to the settings can be
  controlled per role.
- Aligned `setup_version` in `module.xml` with the package version (`1.0.2`).

## [1.0.1] - Prior release

- Initial public version: plugin on `Magento\Newsletter\Model\Subscriber`
  with admin toggles to disable the confirmation request, confirmation success
  and unsubscribe emails.