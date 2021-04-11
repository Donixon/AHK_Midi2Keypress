# AHK_Midi2Keypress + MisteRdeck
I build the MisteRdeck (https://www.thingiverse.com/thing:4627779) and wanted to add scripts to the key-presses. The MisteRdeck is made to use with OBS but I don’t stream. So, I wanted to try to capture the midi-keys and hook an AutoHotkey script to it. Tried some things to make it work like:

- Midi2Key: https://midikey2key.de/ + AutoHotkey o I monitored the keys-press with Midikey2Key and then it would also convert the midi-press to a keystroke like CTRL+ALT+M. After that AutoHotkey would monitor if a CTRL+ALT+M was hit; if so, it would do something (like NEXT in Spotify) This worked semi but the software was instable and would crash a lot. Also, I didn’t like that I needed to kinds of software to make it work.
I was not happy with these results so I looked further to make it work. After that I found Midi2Keypress. It is a fully AHK script that captures a midi-press and with the script you can add keystrokes to it. I wanted to control Spotify with the MisteRdeck, so I did.

# My change's
I added some lines to the AHK script to support the Spotify calls:
```
#InstallKeybdHook
#UseHook
DetectHiddenWindows, On

; Get the HWND of the Spotify main window.
getSpotifyHwnd() {
	WinGet, spotifyHwnd, ID, ahk_exe spotify.exe
	; We need the app's third top level window, so get next twice.
	spotifyHwnd := DllCall("GetWindow", "uint", spotifyHwnd, "uint", 2)
	spotifyHwnd := DllCall("GetWindow", "uint", spotifyHwnd, "uint", 2)
	Return spotifyHwnd
}

; Send a key to Spotify.
spotifyKey(key) {
	spotifyHwnd := getSpotifyHwnd()
	; Chromium ignores keys when it isn't focused.
	; Focus the document window without bringing the app to the foreground.
	ControlFocus, Chrome_RenderWidgetHostHWND1, ahk_id %spotifyHwnd%
	ControlSend, , %key%, ahk_id %spotifyHwnd%
	Return
}
```

Where you can add keystrokes and scripts I added these lines:
```
if (data1 = 8)			; THIS is the item to edit,  put your note number here,  in place of 43
		{
			spotifyKey("{Space}") ;Pause Spotify
			Return
		}
		if (data1 = 5)			; THIS is the item to edit,  put your note number here,  in place of 42
		{
			spotifyKey("^{Right}") ;Next Spotify
			Return
		}
		
		if (data1 = 11)			; THIS is the item to edit,  put your note number here,  in place of 42
		{
			spotifyKey("^{Left}") ;Previous Spotify
			Return
		}

		if (data1 = 9)			; THIS is the item to edit,  put your note number here,  in place of 42
		{
			spotifyKey("^{Up}") ;Volume + Spotify
			Return
		}

		if (data1 = 7)			; THIS is the item to edit,  put your note number here,  in place of 42
		{
			spotifyKey("^{Down}") ;Volume - Spotify
			Return
		}

		if (data1 = 12)			; THIS is the item to edit,  put your note number here,  in place of 42
		{
			spotifyHwnd := getSpotifyHwnd() ; Show spotify
			WinGet, style, Style, ahk_id %spotifyHwnd%
			if (style & 0x10000000) { ; WS_VISIBLE
				WinHide, ahk_id %spotifyHwnd%
			} Else {
				WinShow, ahk_id %spotifyHwnd%
				WinActivate, ahk_id %spotifyHwnd%
			}
			Return
		}
```
