# Network Explorer – aktuell kontext

Sparad på Magnus uttryckliga begäran 2026-10-26.
Besluten här gäller framför äldre historiska uppgifter i CODEX.md och DISTRIBUTION.md.

## Inriktning och identitet

- Magnus har uttryckligen släppt App Store-planerna: **”Glöm App Store!”** Appen ska vara ett praktiskt lokalt Mac-verktyg för nätverksöversikt och snabb SSH-inloggning på hans Raspberry Pi-enheter. Återinför inte sandboxanpassning eller App Store-arbete utan ny begäran.
- Namn: **Network Explorer**. Bundle-ID: `se.egelberg.network-explorer`.
- Lokal repomapp: `/Users/magnus/GitHub/network-explorer`.
- GitHub/origin: `https://github.com/meg768/network-explorer.git`, branch `main`.
- Xcode-projekt och scheme: `network-explorer.xcodeproj` / `network-explorer`.
- Swift-paket, modul och executable: `NetworkExplorer`. Källkod och tester ligger i `Sources/NetworkExplorer` respektive `Tests/NetworkExplorerTests`.
- Installerad app: `/Applications/Network Explorer.app`. macOS 14 eller senare.
- Godkänd ikon: safirblå platta, turkosa/elfenbensfärgade Wi-Fi-bågar, gul punkt och tre enhetssymboler. Behåll den; de minsta storlekarna har en förenklad variant.
- Hjälpsida: `https://meg768.github.io/network-explorer/`, källor i `docs/`. Hjälpmenyn ska bara innehålla Network Explorer Help, utan Search.
- Gamla arkiv, paket och uppladdningsuppgifter i DISTRIBUTION.md är historik för tidigare byggen och identiteter.

## SSH – slutligt önskat beteende

- App Sandbox är avstängd i Debug och Release. Hardened runtime är kvar.
- Automatisk systeminspektion använder macOS OpenSSH och befintliga nycklar, agent, `~/.ssh/config` och vanliga known-hosts-filer.
- Först provas konfigurerat/default Mac-konto, därefter `pi` och `root`. Första lyckade kontot används.
- Bakgrundsinspektion är icke-interaktiv, accepterar nya värdnycklar och avvisar ändrade värdnycklar. Ingen agent-/portvidarebefordran eller lokal SSH-kommandokörning aktiveras av bakgrundsinspektionen.
- **Ingen Settings-scen, SSH-inställningsdialog, verktygsfältsknapp för inställningar, nyckelimport eller appägd lösenfraslagring.** Magnus avvisade sådana flöden uttryckligen.
- **Open in Terminal** ligger på **SSH-raden under Services** när den inspekterade SSH-porten 22 svarar. Lyckad lösenordsfri inloggning är inte ett krav för knappen.
- Knappen är vänsterjusterad med extra luft: 8 punkters övre och 4 punkters undre padding utöver radens vanliga mellanrum. Centrering provades och valdes bort.
- Efter lyckad inspektion används det faktiska SSH-kontot. Annars öppnas adressen utan explicit användare, så OpenSSH väljer konfigurerat/default Mac-konto.
- Knappen öppnar en `ssh://`-URL i Apples Terminal via NSWorkspace. Användaren kan själv ange lösenord där. Appen frågar inte efter lösenord.
- Knappen ska inte samtidigt visas vid ”SSH passwordless login” under System information; själva resultattexten ligger kvar där.
- Användarnamn och IPv4-adress valideras innan Terminal-URL skapas. Inga genererade shellskript eller AppleScript behövs.

## Enhetsidentifiering och befintlig funktion

- Tesla-prefixet `4c:fc:aa` känns igen som Tesla. Nätverksnamn såsom `tesla_model_3` ger ”Tesla Model 3 / electric vehicle candidate”. Även modellnamnen S, X, Y, Cybertruck och Roadster stöds när de finns i ett tydligt Tesla-namn.
- Modellidentifieringen är en gissning utifrån namnet. Tesla-tillverkare utan modellnamn ger ”Tesla device”, eftersom enheten även kan vara annan Tesla-utrustning. Ingen fordonsdata eller Fleet API-integration har lagts till.
- Verifierat på Magnus Tesla: namn och MAC tillgängliga, ping svarar, inga undersökta tjänster svarade. Batteri, räckvidd och annan fordonstelemetri har inte hämtats.
- Mac-upptäckt ska bevaras. Mac-Mini-2026, MacBook-Air och MacBook-Air-2025 har verifierats i listan. Återställ inte hela projektet till äldre LAN-versioner.
- Behåll den befintliga tabellen med Address / Name / Vendor / Device Type, sortering, filtrering, stabilt val och permanent detaljpanel.
- Skanning gäller privat IPv4 `/24`, adresser .1–.254, med ping, ARP och Bonjour/mDNS-namn före DNS-fallback. Automatisk skanning har 30 sekunders paus efter avslutad skanning.
- Tidigare enheter och vissa tidigare värden behålls avsiktligt under sessionen. Inspektionsresultat cachas per enhet i sessionen.

## Kod, verifiering och Git

- `ContentView.swift`: tabell/detaljer, Services och Terminal-knapp.
- `NetworkScanner.swift`: upptäckt, namn, tjänster, SSH-resultat och Tesla-heuristik.
- `SSHConfiguration.swift`: argument för bakgrunds-SSH och validerad Terminal-URL.
- `Models.swift`: bland annat `DeviceInspectionItem.sshURL` för ett lyckat SSH-konto.
- Senaste verifiering: native Release-bygge, strikt signaturkontroll av installerad app och **12 Swift-tester** godkända. Tesla, Macar, lyckad Raspberry Pi-inspektion och knappens placering har kontrollerats i GUI.
- Terminalstart via knappen verifierades. CUA blockerar åtkomst till Terminal-appen; den interaktiva inloggningen i Terminal kunde därför inte bekräftas med UI-verktyget. Försök inte kringgå verktygets begränsning.
- Vid CUA-start direkt efter installation har första automatiska skanningen visat ”Local network access is unavailable”; manuell Scan Network har därefter fungerat. Detta har inte ändrats i koden.
- Bygg: `xcodebuild -project network-explorer.xcodeproj -scheme network-explorer -configuration Release -destination 'generic/platform=macOS' build`.
- Tester: `swift test`.
- Senast incheckat och pushat före denna kontextspara: `d246204` – ”Identify Tesla devices and add SSH Terminal access”, tagg `2026-10-26-02-03` (Europe/Stockholm). Alla dåvarande ändringar ingick och arbetskatalogen var ren.
- Denna kontextuppdatering är sparad lokalt. Ingen ny commit, tagg eller push ingår i ”spara kontext”.
- Commit/tagg/push kräver uttrycklig begäran. ”checkin”/”check in” betyder alla icke-ignorerade projektändringar, relevanta kontroller, en ny tidsstämpeltagg `YYYY-MM-DD-HH-MM` i Europe/Stockholm och push av aktuell branch och just den nya taggen. Aldrig force-push eller överskrivning av taggar.
