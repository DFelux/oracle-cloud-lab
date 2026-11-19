# Oracle Cloud Lab

Linux-Server in der Oracle Cloud (OCI Always Free) als Ergänzung zu meinem [IT Infrastructure Lab](https://github.com/DFelux/it-infrastructure-lab).
Das Homelab läuft auf Ubuntu (Debian-Familie), diese VM auf **Oracle Linux 9** (RHEL-kompatibel). Ziel: Linux-Administration in beiden großen Familien üben und eine öffentlich erreichbare Cloud-VM sicher betreiben.

> Status: **im Aufbau** (Start 15.11.2026)

## Umgebung

| Bereich | Wert |
|---|---|
| Anbieter / Region | Oracle Cloud Infrastructure, eu-zurich-1 (Always Free) |
| Instanz | `VM-lab-micro-02` (Neuaufbau 19.11.), Shape `VM.Standard.E2.1.Micro` (1 OCPU, 1 GB RAM, x86_64) |
| Betriebssystem | Oracle Linux Server 9 (`platform:el9`), Kernel UEK 8 |
| Netz | VCN `vcn-lab-micro-01`, öffentliches Subnetz `subnet-micro-public-01` (10.0.0.0/24) |
| Zugriff | SSH nur per Schlüssel, Benutzer `opc`, öffentliche IP (ephemeral, hier nicht veröffentlicht) |
| Speicher | Swap 2,5 GiB (Standard 0,5 GiB + 2 GiB), Boot Volume 46,6 GB, Verschlüsselung durch Oracle verwaltet, In-Transit-Verschlüsselung aktiv |
| Agent-Plugins | Instance Monitoring, Run Command (Dynamic Group vorbereitet), Cloud Guard Workload Protection; Custom Logs auf E2.1.Micro nicht unterstützt |
| Notfallzugang | serielle Konsole (Passwort für `opc`), Boot-Volume-Backup vor Updates |

## Kostenkontrolle

- Konto bewusst im **Free Tier** belassen (kein Upgrade auf Pay As You Go): ohne Upgrade keine Abrechnung über die Always-Free-Grenzen hinaus.
- **Budget mit Warnung** (Actual Spend, 1 €) als zweite Absicherung. OCI kennt keine harte Kostengrenze, Budgets warnen nur und nicht in Echtzeit.
- Nur Ressourcen mit Kennzeichnung „Always Free-eligible“; RHEL- und Windows-Images (Lizenzkosten) bewusst nicht gewählt.

## Kapitel

| Nr. | Thema | Status |
|---|---|---|
| 01 | [Einrichtung: Konto, Netz, Instanz, SSH](docs/01-einrichtung.md) | erledigt |
| 02 | [Störung: SSH-Timeout – Eingrenzung mit Path Analyzer](docs/02-stoerung-ssh-timeout.md) | geschlossen |
| 03 | [Neuaufbau mit Backup, Notfallzugang und Swap](docs/03-neuaufbau.md) | erledigt |
| 04 | SSH-Härtung (eigener Admin-Benutzer, nur Schlüssel, kein Root-Login) | geplant |
| 05 | Host-Firewall mit firewalld, Security List auf eigene IP | geplant |
| 06 | Vergleich Ubuntu ↔ Oracle Linux (apt/dnf, ufw/firewalld, SELinux) | geplant |

## Bekannte Grenzen

- Ampere A1 (Arm, mehr RAM) war bei Erstellung nicht verfügbar („Out of capacity“, Zürich hat nur eine Availability Domain). Die A1-Konfiguration ist als Resource-Manager-Stack (Terraform) gespeichert; ein erneuter Versuch am 19.11. scheiterte ebenfalls („Out of host capacity“).
- E2.1.Micro hat sehr wenig Arbeitsspeicher: `dnf update` braucht erweiterten Swap und den Ausschluss großer Repositories.
- Always-Free-Instanzen mit dauerhaft geringer Auslastung können von Oracle zurückgefordert werden.
