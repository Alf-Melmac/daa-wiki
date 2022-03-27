# Server Setup

## Checkliste

* [ ] Aktuelle Version des [Performance Branches](https://forums.bohemia.net/forums/topic/160288-arma-3-stable-server-208-performance-binary-feedback/) installiert (außer dedmen empfiehlt eine andere Version)
* [ ] [#konfiguration](server-setup.md#konfiguration "mention") übernommen
* [ ] [#streamator](server-setup.md#streamator "mention") installiert
* [ ] Keycheck aktiviert
  * [ ] [#clientseitige-mods](server-setup.md#clientseitige-mods "mention") erlaubt
* [ ] Server IP, Port und Passwort Alf mitgeteilt
  * [ ] Techcheck Server
  * [ ] Event Server
* [ ] Server ist eine Woche vor Missionsbeginn zum Techcheck bereit

## Performance Branch

Durch den Performance Branch können wir von den aktuellsten Verbesserungen aus der Entwicklung von Arma 3 profitieren. Auch wenn dies Vorveröffentlichungen sind, durchlaufen diese Versionen eine QA-Phase bei BI und sollten von uns verwendet werden.

Installation über steamcmd. Unter Linux ist die Syntax äquivalent

```powershell
steamcmd.exe +login %STEAMLOGIN% +force_install_dir %A3serverPath% +app_update 233780 -beta profiling -betapassword CautionSpecialProfilingAndTestingBranchArma3 validate +quit
```

Durch die Ausführung dieses Befehls wird im angegebenen Ordner die `arma3server_x64` Ausführungsdatei mit dem aktuellen Performance Build ersetzt. Über den alternativen Google Drive Download ersetzt ihr diese natürlich selbst.

Die zusätzliche `arma3serverprofiling_x64` Ausführungsdatei enthält mehr Logausgaben für die Auswertung von BI. Diese nur nach Absprache verwenden, da teils ressourcenintensive Berechnungen ausgeführt werden.

## Konfiguration

Die Standard-Einstellungen werden teils durch die [DAA Mod](https://github.com/dedmen/DAAMod/blob/main/settings/cbasettings.sqf) (z.B. Schwierigkeit) gesetzt, andererseits durch die unsere [Standard-Konfigurationen](https://www.deutsche-arma-allianz.de/cloud/index.php/s/jf8M1Sgrk2eQ0vW?path=%2FSettings).

#### Server Start Parameter

`-noSound -bandwidthAlg=2 -enableHT -hugePages -limitFPS=200`

``

{% code title="basic.cfg" %}
```editorconfig
///////////////////////////////////////////////////////////////////////////////
//     Arma 3 - basic.cfg
//     See https://community.bistudio.com/wiki/basic.cfg
///////////////////////////////////////////////////////////////////////////////

///////////////////////////////////////////////////////////////////////////////
// Network Tuning 
///////////////////////////////////////////////////////////////////////////////
// Wert in bps - bei einer 1 Gbit/s Anbindung - 80% min
MinBandwidth = 800000000;

MaxMsgSend = 1024;          		// Original: 128

MaxSizeGuaranteed = 1024;     		// Original: 512
MaxSizeNonguaranteed = 512;   		// Original: 256

MinErrorToSend = 0.002;			// Original: 0.001
MinErrorToSendNear = 0.02;   		// Original: 0.01

MaxCustomFileSize = 0;


///////////////////////////////////////////////////////////////////////////////
// DAA erzwungen
///////////////////////////////////////////////////////////////////////////////
//MaxBandwidth = xxxxxxxx;		// !!! EXTRA AUS !!!

class sockets {
    maxPacketSize = 1430;		// 1430 MTU
    
    initBandwidth = 2000000;	        // DSL 16000 -> 2000000 (value in byte/s)
    //MinBandwidth = xxxxx;		// !!! EXTRA AUS !!!
    MaxBandwidth = 3750000;		// VDSL 30000 -> 6250000 (value in byte/s)
};
```
{% endcode %}

####

{% code title="server.cfg" %}
```editorconfig
//
// server.cfg
//
// comments are written with "//" in front of them.
//
// NOTE: More parameters and details are available at http://community.bistudio.com/wiki/server.cfg

// SECURITY
admins[]			= {};
BattlEye			= 0;	// If set to 1, BattlEye Anti-Cheat will be enabled on the server (default: 1, recommended: 1)
verifySignatures		= 2;	// If set to 2, players with unknown or unsigned mods won't be allowed join (default: 0, recommended: 2)
kickDuplicate			= 1;	// If set to 1, players with an ID that is identical to another player will be kicked (recommended: 1)
allowedFilePatching		= 1;	// Prevents clients with filePatching enabled from joining the server
					// (0 = block filePatching, 1 = allow headless clients, 2 = allow all) (default: 0, recommended: 1)

// TIMEOUTS
maxDesync = 200;			// Max desync value until server kick the user
maxPing = 300;				// Max ping value until server kick the user
maxPacketLoss = 10;			// Max packetloss value until server kick the user
kickClientsOnSlowNetwork[] = { 1, 1, 0, 1 }; // Defines if {<MaxPing>, <MaxPacketLoss>, <MaxDesync>, <DisconnectTimeout>} will be logged (0) or kicked (1)
kickTimeout[] = { {0, 0}, {1, 0}, {2, 0}, {3, 0} };
disconnectTimeout = 7;			// Time to wait before disconnecting a user which temporarly lost connection. Range is 5 to 90 seconds.
armaUnitsTimeout = 5;

//Chat
disableVoN = 1;				// If set to 1, voice chat will be disabled
vonCodec = 0;
vonCodecQuality = 0;			// Supports range 1-30, the higher the better sound quality, the more bandwidth consumption:
					// 1-10 is 8kHz (narrowband)
					// 11-20 is 16kHz (wideband)
					// 21-30 is 32kHz (ultrawideband)
disableChannels[] = {{0,false,true}, {1,false,true}, {2,false,true}, {3,false,true}, {4,false,true}, {5,false,true}, {6,false,true}}; // Chat aus

//Network
steamProtocolMaxDataSize = 2048;	// oder höher

persistent = 1;				// If set to 1, missions will continue to run after all players have disconnected; required if you want to use the -autoInit startup parameter
zeusCompositionScriptLevel = 2;		//Alle scripts für zeus compositions erlauben
```
{% endcode %}

## Streamator

Für die DAA Streams auf YouTube und Twitch benötigen wir in jeder Mission [zwei virtuelle Zuschauerplätze](https://wiki.tacticalteam.de/de/TTT-QM/Skriptsammlung#streamator). Auf dem Server muss CLib und [Streamator](https://github.com/TaktiCool/Streamator) installiert sein.

{% file src="../.gitbook/assets/CLib.zip" %}
[https://github.com/TaktiCool/CLib](https://github.com/TaktiCool/CLib)
{% endfile %}

{% file src="../.gitbook/assets/Streamator.zip" %}
[https://github.com/TaktiCool/Streamator](https://github.com/TaktiCool/Streamator)
{% endfile %}

## Clientseitige Mods

Zusätzlich zu dem Standard  und dem Zusatz der jeweiligen Mission sind folgende clientseitige Modifikationen erlaubt und dem Keycheck hinzuzufügen:

| Mod                    | Workshop-Link                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Soundmod (JSRS)        | <p>[<code>861133494</code>] <a href="https://steamcommunity.com/workshop/filedetails/?id=861133494">JSRS SOUNDMOD</a></p><p>[<code>945476727</code>] <a href="https://steamcommunity.com/sharedfiles/filedetails/?id=945476727">JSRS SOUNDMOD - RHS AFRF Mod Pack Sound Support</a></p><p>[<code>1180533757</code>] <a href="https://steamcommunity.com/sharedfiles/filedetails/?id=1180533757">JSRS SOUNDMOD - RHS USAF Mod Pack Sound Support</a></p><p>[<code>1180534892</code>] <a href="https://steamcommunity.com/sharedfiles/filedetails/?id=1180534892">JSRS SOUNDMOD - RHS GREF Mod Pack Sound Support</a></p><p>[<code>1486541773</code>] <a href="https://steamcommunity.com/sharedfiles/filedetails/?id=1486541773">JSRS SOUNDMOD - RHS SAF Mod Pack Support</a></p> |
| Visual Mod (Blastcore) | \[`2257686620`] [Blastcore Murr Edition](https://steamcommunity.com/sharedfiles/filedetails/?id=2257686620)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Auto Pilots            | \[`1439605692`] [Realistic Auto Pilots](https://steamcommunity.com/sharedfiles/filedetails/?id=1439605692)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| TrackIR                | \[`630737877`] [Head Range Plus - TrackIR Mod](https://steamcommunity.com/sharedfiles/filedetails/?id=630737877)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
