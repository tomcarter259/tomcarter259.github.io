# Pull Request Reviews

Reviewed on: 2026-03-07

## PR #10 — Bump tzinfo from 1.2.6 to 1.2.10

**Branch:** `dependabot/bundler/tzinfo-1.2.10`
**Author:** dependabot[bot]
**Status:** Open (already merged to master)

### Summary
Updates `tzinfo` from 1.2.6 to 1.2.10 in `Gemfile.lock`. This is a security patch addressing **CVE-2022-31163**, a relative path traversal vulnerability that could allow arbitrary file reads via `TZInfo::Timezone.get` when used with `RubyDataSource`.

### Changes
- `Gemfile.lock`: `tzinfo` 1.2.6 → 1.2.10

### Review
- **Risk:** Low. Lockfile-only change, patch version bump within the same minor series.
- **Security:** Fixes a real CVE. The vulnerability allows directory traversal when timezone identifiers contain `..` sequences.
- **Compatibility:** No breaking changes expected.
- **Note:** This update appears to already be applied to master.

### Recommendation: **Approve**
This PR can be safely merged (or closed if already applied). Straightforward security fix with no compatibility concerns.

---

## PR #12 — Bump activesupport from 6.0.3.1 to 6.0.6.1

**Branch:** `dependabot/bundler/activesupport-6.0.6.1`
**Author:** dependabot[bot]
**Status:** Open

### Summary
Updates `activesupport` from 6.0.3.1 to 6.0.6.1. This addresses **CVE-2023-22794**, a SQL comment injection vulnerability in Active Record's `sanitize_as_sql_comment` method where specially crafted input could circumvent sanitization.

### Changes
- `Gemfile.lock`:
  - `activesupport` 6.0.3.1 → 6.0.6.1
  - `concurrent-ruby` 1.1.6 → 1.2.0
  - `minitest` 5.14.1 → 5.17.0
  - `zeitwerk` 2.3.0 → 2.6.6
  - `tzinfo` 1.2.10 → 1.2.6 (**regression — see below**)

### Review
- **Risk:** Low-medium. Patch version bump with several transitive dependency updates, but all within compatible semver ranges.
- **Security:** Fixes CVE-2023-22794. Although this Jekyll site likely doesn't use Active Record directly, keeping dependencies patched is good hygiene.
- **Compatibility:** No breaking changes expected for a Jekyll site.
- **Conflict:** This PR **downgrades `tzinfo` from 1.2.10 to 1.2.6**, which would revert the security fix from PR #10. This must be resolved before merging.

### Recommendation: **Approve after rebase**
The security fix is valuable. However, this PR needs to be rebased on current master to avoid reverting the tzinfo security patch. Use `@dependabot rebase` to trigger a refresh.

---

## PR #13 — Bump nokogiri from 1.13.6 to 1.14.3

**Branch:** `dependabot/bundler/nokogiri-1.14.3`
**Author:** dependabot[bot]
**Status:** Open

### Summary
Updates `nokogiri` from 1.13.6 to 1.14.3. This addresses multiple security vulnerabilities in vendored libxml2:
- **CVE-2023-29469** — libxml2 hashing randomization issue
- **CVE-2023-28484** — libxml2 NULL dereference in xmlSchemaFixupComplexType

### Changes
- `Gemfile.lock`:
  - `nokogiri` 1.13.6 → 1.14.3
  - `mini_portile2` 2.8.0 → 2.8.1
  - `racc` 1.6.0 → 1.6.2
  - `tzinfo` 1.2.10 → 1.2.6 (**regression — see below**)

### Review
- **Risk:** Medium. Nokogiri 1.14.x drops support for Ruby 2.6 and JRuby 9.3. Verify the site's Ruby version is >= 2.7.
- **Security:** Fixes critical CVEs in libxml2, which is a core XML/HTML parsing dependency. Nokogiri is heavily used by Jekyll for HTML processing.
- **Compatibility:** If the site uses Ruby >= 2.7, no issues expected. Check with `ruby --version`.
- **Conflict:** Like PR #12, this PR **downgrades `tzinfo` from 1.2.10 to 1.2.6**, reverting the fix from PR #10.

### Recommendation: **Approve after rebase and Ruby version check**
Critical security fixes for a core dependency. Before merging:
1. Verify Ruby version is >= 2.7
2. Rebase on current master to preserve the tzinfo fix (use `@dependabot rebase`)

---

## Overall Recommendations

1. **Merge PR #10 first** (or close it if already applied to master) — it's the simplest and has no conflicts.
2. **Rebase PRs #12 and #13** using `@dependabot rebase` so they pick up the current tzinfo version.
3. **Merge PR #13 (nokogiri)** next — it fixes the most critical CVEs.
4. **Merge PR #12 (activesupport)** last.
5. Consider enabling Dependabot auto-merge for patch-level security updates to prevent PRs from going stale.
