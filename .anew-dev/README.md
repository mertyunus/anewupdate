# .anew-dev/

ANEW's own development records: the specs and plans that change the template itself.
They follow the same rules as a project's `specs/` (`specs/TEMPLATE.md`, `specs/plans/TEMPLATE.md`,
AGENTS.md invariant rules) but live here so the template's `specs/` stays empty for its users.

- `specs/NNNN-<name>.md` — active specs, one per piece of work; numbering is shared with `plans/`
  and `done/`.
- `plans/NNNN-plan.md` — the approved plan, `Approved by / on` is the gate record.
- Shipped specs move to `done/` and are never edited again.
- `docs/testing.md` rule 6 (a ship commit touching only `specs/` inherits the full green) applies
  here to `.anew-dev/`: `git diff --name-only <green-sha> HEAD -- . ':!.anew-dev/'` prints nothing.

- doctor scans `specs/`, not `.anew-dev/`: ANEW's own ships are checked by the reviewer and
  VERIFY against the same rules (ticked or moved-out criteria, open targets), not by doctor. In rule 6
  doctor's judgment of the ship is replaced by this: before the ship commit, the human (or a
  reviewer on the ship diff) checks the `done/` file against those rules, with the diff shown;
  only then does the `.anew-dev/` ship commit inherit the full green.

## Releases and the LTS line

- Every release is an annotated tag on the commit that ships it (message: its CHANGELOG heading),
  pushed by name; its GitHub release carries the CHANGELOG entry.
- `lts/1.24` starts at the v1.24.1 tag and takes fixes only (`README.md`, Versions and support).
- A fix lands on `main` first, through its own spec. It reaches `lts/1.24` by
  `git cherry-pick -x <main commit>` in a PR with CI green. Its `.anew-dev/` hunks are dropped;
  its `CHANGELOG.md` and `.anew-version` hunks are replaced by the branch's own `v1.24.<n+1>`
  entry and marker. After merge the tag `v1.24.<n+1>`; then `main`'s entry for that fix gets
  "also in v1.24.<n+1>".
- If the fix's other files do not apply cleanly, it is redone on the branch, never forced. Every
  fix on `lts/1.24` names its `main` commit (`cherry picked from`; a redone fix writes that line
  by hand): no fix reaches the line that `main` does not already hold.

## Çalışma kararları (karar kaydı 2026-11-12)

Bu depo ANEW şablonunun kaynağıdır; ürünü şablonun kendisidir. Bu yüzden **bootstrap edilmez**
(karar: Yunus Mert, 2026-11-12, R-10 seçenek A): `AGENTS.md` ve `docs/*.md` şablonu kullananlar
için boş kalır. **Bu depoda `AGENTS.md`'nin "STATUS: NOT CONFIGURED … ilk iş bootstrap" satırı ve
doctor'ın bootstrap uyarıları geçersizdir**; geri kalan her şey (değişmez kurallar dahil) aynen
geçerlidir. ANEW'i iyileştiren her işin spec, plan ve kayıtları bu klasörde tutulur ve aşağıdaki
kararlara uyar. Kök `README.md` ("Developing ANEW itself") buraya yönlendirir.

- **Language:** `chat=tr · docs=tr`. Kural: `.anew-dev/` dışındaki her dosya İngilizcedir (şablonla
  kullanıcıya gider). `.anew-dev/` içinde yeni metin Türkçe yazılır; mevcut İngilizce metin olduğu
  gibi kalır. Protokol alanları ve değerleri (`Status:`, `Approved by / on:`, `Source:`, `Mode:`,
  şablon başlıkları) her yerde İngilizcedir.
- **Mode:** `lite` — her spec'in başlık alanı `- Mode: lite` (örnek: `done/0005-lts.md`); bağımsız
  review ve VERIFY zorunlu.
- **Yeşil tanımı:** bu depoda `scripts/check.conf` yoktur; `./scripts/check` exit 1 verir ve CI'ın
  `check` işi adımı atlar. Bu yüzden kural 4'teki ve `docs/git.md`'deki "`scripts/check` yeşil",
  burada **HEAD SHA'sında CI `doctor` işinin (`doctor --strict` + `selftest`) yeşil olması**
  demektir; kanıt = CI run linki + SHA (yerel ek kanıt: `selftest` sayımı). `docs/testing.md`
  kural 6 yalnızca yukarıda anlatılan, sadece `.anew-dev/`'e dokunan ship commit'i için geçerlidir.
- **Trivial changes:** politika (1) — PR + bir reviewer, spec yok. Trivial = değişiklik talebi
  triage'ının (`workflows/change-request.md`) TRIVIAL saydığı, yalnızca Markdown'da yazım, link
  veya biçim düzeltmesi. Her zaman: dal `trivial/<ad>`, tek dar commit, tek dosya, yukarıdaki yeşil.
  İkinci dosya veya yeni test → yeniden triage. Trivial **değildir** → spec: `scripts/` altındaki
  her değişiklik, kural veya kapı anlamına dokunan her cümle, `gate-enforcement` kapsamındaki her
  dosya ve `done/` altındaki her şey (gönderilmiş spec'ler değişmez, kural 7).
- **Sensitive area: `gate-enforcement`.** Kapsam:
  - `scripts/check`, `scripts/doctor`, `scripts/residue`, `scripts/selftest`, `scripts/init`;
  - `.github/workflows/check.yml`; `adapters/claude-code/.claude/hooks/protect-shipped.sh`;
  - kapı alanlarını ve işaretlerini tanımlayan metin: `specs/TEMPLATE.md`, `specs/TEMPLATE-mini.md`,
    `specs/plans/TEMPLATE.md`, `workflows/segments.md`, `docs/commands.md`;
  - ve doctor'ın ya da CI'ın okuduğu her alanı veya işareti tanımlayan her metin (`Status:`,
    `Approved by / on:`, `Mode:`, `Self-critique:`, `Sensitive area:`, `Source:`, `→ moved to B-<n>` …).

  Neden: fail-open bir hata, şablonu kullanan tüm projelerin kapılarını sessizce kaldırır.
  **`.anew-dev/` işlerinde bu liste `docs/domain.md` "Sensitive areas" bölümünün yerini tutar.**
  doctor `.anew-dev/`'i taramaz; kurallar reviewer, VERIFY ve insanın ship kontrolü tarafından elle
  uygulanır: lite'ta da clarify kapısı sorulur; yalnızca belge değiştiren turlar dışında her düzeltme
  turu yeniden review edilir; her kriter bir kasıtlı bozmayla kırmızıya düşürülerek kanıtlanır;
  one-pass review yoktur; bu alandaki bir kriter dışarı taşınamaz. Bildirim biçimi: spec'in
  Constraints bölümünde kendi satırında `- Sensitive area: gate-enforcement` (cümle, tablo, başlık
  veya alıntı içinde değil).
- **Git:** iş dalı → PR → yukarıdaki yeşil + bir onay → `main`; `main`'e doğrudan commit yok.

Template users: this folder is not part of your workspace — delete it after you create your repo.
