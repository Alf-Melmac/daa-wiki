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

Durch die Ausführung dieses Befehls wird im angegebenen Ordner die `arma3server_x64` Ausführungsdatei mit dem aktuellen Performance Build ersetzt.
Es gibt auch die Möglichkeit, die Dateien manuell über das alternative [Google Drive](https://drive.google.com/drive/folders/15p9j7C2nHUt6NoVfChX4YFuqzFXzblJh) Angebot herunterzuladen.

Die zusätzliche `arma3serverprofiling_x64` Ausführungsdatei enthält mehr Logausgaben für die Auswertung von BI. Diese nur nach Absprache verwenden, da teils ressourcenintensive Berechnungen ausgeführt werden.

## Konfiguration

Die Standard-Einstellungen werden teils durch die [DAA Mod](https://github.com/dedmen/DAAMod/blob/main/settings/cbasettings.sqf) (z.B. Schwierigkeit) gesetzt, andererseits durch die unsere [Standard-Konfigurationen](https://www.deutsche-arma-allianz.de/cloud/index.php/s/jf8M1Sgrk2eQ0vW?path=%2FSettings).

#### Server Start Parameter

`-noSound -bandwidthAlg=2 -enableHT -hugePages -limitFPS=200`

``

{% code title="basic.cfg" %}
```editorconfig
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
// Die hier aufgelisteten Einstellungen sind von DAA erzwungen. Die übrigen Einstellungen können frei gewählt werden.

// SECURITY
admins[]			= {};
BattlEye			= 0;	// Wir deaktivieren BattlEye, da es zu viel Performancelast erzeugt und wir es nicht benötigen
verifySignatures		= 2;	// Signaturverifikation aktivieren, damit Spieler keine falschen Mods mitladen können
kickDuplicate			= 1;	// Kicke Spieler mit der selben SteamID, sollte ein Spieler doppelt connecten
allowedFilePatching		= 1;	// Erlaube FilePatching nur für Headless Clients. Könnte auch auf 0 stehen, um es komplett zu deaktivieren. Dies hat aber keine Vorteile

// TIMEOUTS
maxDesync = 200;			// Maximaler Desync Wert bis der Server den Spieler kickt
maxPing = 300;				// Maximaler Ping Wert (in Millisekunden) bis der Server den Spieler kickt
maxPacketLoss = 10;			// Maximaler Paketverlust Wert bis der Server den Spieler kickt
kickClientsOnSlowNetwork[] = { 1, 1, 0, 1 }; // Defines if {<MaxPing>, <MaxPacketLoss>, <MaxDesync>, <DisconnectTimeout>} will be logged (0) or kicked (1)
kickTimeout[] = { {0, 0}, {1, 0}, {2, 0}, {3, 0} }; // Diese Werte ändern eine Kick in einen temporären Ban. Das wollen wir nicht, also alles 0 Sekunden
disconnectTimeout = 7;			// Time to wait before disconnecting a user which temporarly lost connection. Range is 5 to 90 seconds.
armaUnitsTimeout = 5;

//Chat
disableVoN = 1;				// Wir benutzen keinen ingame Voice Chat. Das erzeugt sowohl Netzwerk-Traffic, als auch Serverlast und verwirrung bei Spielern, wenn diese ihr Capslock-Keybind nicht rausgenommen haben

disableChannels[] = {{0,false,true}, {1,false,true}, {2,false,true}, {3,false,true}, {4,false,true}, {5,false,true}, {6,false,true}}; // Wir deaktivieren den Chat komplett, aber! wir lassen Voicechat aktiviert. Wenn sowohl Text- als auch Voice-chat deaktiviert sind, kann man die Chat Channel nicht mehr anwählen um zum Beispiel Marker in einem spezifischen Channel zu setzen.

//Network
steamProtocolMaxDataSize = 8192;	// Das ist das Größenlimit der Daten, die der Server an den Arma Launcher senden kann. Dieser Wert kann nicht zu hoch sein, aber wenn er zu niedrig ist, passt das Modpack nicht in die Daten und im Arma Launcher bekommt man unser volles Modpack nicht angezeigt

persistent = 1;				// Mission wird nicht beendet/resetted wenn alle Spieler disconnecten
zeusCompositionScriptLevel = 2;		//Alle Scripts für zeus compositions erlauben
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
