Google Speech recognition in VitalPBX
=====

This script makes use of Google's Cloud Speech API in order to render speech
to text and return it back to the dialplan as an asterisk channel variable.

## Requirements<br>
Perl          The Perl Programming Language<br>
perl-libwww   The World-Wide Web library for Perl<br>
perl-libjson  Module for manipulating JSON-formatted data<br>
IO-Socket-SSL Perl module that implements an interface to SSL sockets.<br>
flac          Free Lossless Audio Codec<br>

Cloud Speech API key from Google (https://cloud.google.com/speech).
Internet access in order to contact Google and get the speech data.

## Installation<br>
To install copy speech-recog.agi to your agi-bin directory.
Usually this is /var/lib/asterisk/agi-bin/
To make sure check your /etc/asterisk/asterisk.conf file

## Usage<br>
agi(speech-recog.agi,[lang],[timeout],[intkey],[NOBEEP],[rtimeout],[speechContexts])
Records from the current channel until 2 seconds of silence are detected
(this can be set by the user by the 'timeout' argument, -1 for no timeout) or the
interrupt key (# by default) is pressed. If NOBEEP is set, no beep sound is played
back to the user to indicate the start of the recording. If 'rtimeout' is set, 
overwrite to the absolute recording timeout. 'SpeechContext' provides hints to 
favor specific words and phrases in the results. Usage: [Agamemnon,Midas]
The recorded sound is send over to Google speech recognition service and the
returned text string is assigned as the value of the channel variable 'utterance'.
The scripts sets the following channel variables:

utterance  : The generated text string.
confidence : A value between 0 and 1 indicating the probability of a correct recognition.
             Values bigger than 0.95 usually mean that the resulted text is correct.

In case of an unexpected error both these variables are set to '-1'.

## Examples<br>
sample dialplan code for your extensions.conf

<pre>
;Simple speech recognition
exten => 1234,1,Answer()
exten => 1234,n,agi(speech-recog.agi,en-US)
exten => 1234,n,Verbose(1,The text you just said is: ${utterance})
exten => 1234,n,Verbose(1,The probability to be right is: ${confidence})
exten => 1234,n,Hangup()

;Speech recognition demo also using googletts.agi for text to speech synthesis:
exten => 1235,1,Answer()
exten => 1235,n,agi(googletts.agi,"Say something in English, when done press the pound key.",en)
exten => 1235,n(record),agi(speech-recog.agi,en-US)
exten => 1235,n,Verbose(1,Script returned: ${confidence} , ${utterance})

;Check the probability of a successful recognition:
exten => 1235,n,GotoIf($["${confidence}" > "0.8"]?playback:retry)

;Playback the text
exten => 1235,n(playback),agi(googletts.agi,"The text you just said was...",en)
exten => 1235,n,agi(googletts.agi,"${utterance}",en)
exten => 1235,n,goto(end)

;Retry in case speech recognition wasn't successful:
exten => 1235,n(retry),agi(googletts.agi,"Can you please repeat more clearly?",en)
exten => 1235,n,goto(record)

exten => 1235,n(fail),agi(googletts.agi,"Failed to get speech data.",en)
exten => 1235,n(end),Hangup()

;Voice dialing example
exten => 1236,1,Answer()
exten => 1236,n,agi(googletts.agi,"PLease say the number you want to dial.",en)
exten => 1236,n(record),agi(speech-recog.agi,en-US)
exten => 1236,n,GotoIf($["${confidence}" > "0.8"]?success:retry)

exten => 1236,n(success),goto(${utterance},1)

exten => 1236,n(retry),agi(googletts.agi,"Can you please repeat?",en)
exten => 1236,n,goto(record)
</pre>

## Supported Languages
"af-ZA" Afrikaans (Suid-Afrika)<br>
"id-ID" Bahasa Indonesia (Indonesia)<br>
"ms-MY" Bahasa Melayu (Malaysia)<br>
"ca-ES" Català (Espanya)<br>
"cs-CZ" Čeština (Česká republika)<br>
"da-DK" Dansk (Danmark)<br>
"de-DE" Deutsch (Deutschland)<br>
"en-AU" English (Australia)<br>
"en-CA" English (Canada)<br>
"en-GB" English (Great Britain)<br>
"en-IN" English (India)<br>
"en-IE" English (Ireland)<br>
"en-NZ" English (New Zealand)<br>
"en-PH" English (Philippines)<br>
"en-ZA" English (South Africa)<br>
"en-US" English (United States)<br>
"es-AR" Español (Argentina)<br>
"es-BO" Español (Bolivia)<br>
"es-CL" Español (Chile)<br>
"es-CO" Español (Colombia)<br>
"es-CR" Español (Costa Rica)<br>
"es-EC" Español (Ecuador)<br>
"es-SV" Español (El Salvador)<br>
"es-ES" Español (España)<br>
"es-US" Español (Estados Unidos)<br>
"es-GT" Español (Guatemala)<br>
"es-HN" Español (Honduras)<br>
"es-MX" Español (México)<br>
"es-NI" Español (Nicaragua)<br>
"es-PA" Español (Panamá)<br>
"es-PY" Español (Paraguay)<br>
"es-PE" Español (Perú)<br>
"es-PR" Español (Puerto Rico)<br>
"es-DO" Español (República Dominicana)<br>
"es-UY" Español (Uruguay)<br>
"es-VE" Español (Venezuela)<br>
"eu-ES" Euskara (Espainia)<br>
"fil-PH" Filipino (Pilipinas)<br>
"fr-FR" Français (France)<br>
"gl-ES" Galego (España)<br>
"hr-HR" Hrvatski (Hrvatska)<br>
"zu-ZA" IsiZulu (Ningizimu Afrika)<br>
"is-IS" Íslenska (Ísland)<br>
"it-IT" Italiano (Italia)<br>
"lt-LT" Lietuvių (Lietuva)<br>
"hu-HU" Magyar (Magyarország)<br>
"nl-NL" Nederlands (Nederland)<br>
"nb-NO" Norsk bokmål (Norge)<br>
"pl-PL" Polski (Polska)<br>
"pt-BR" Português (Brasil)<br>
"pt-PT" Português (Portugal)<br>
"ro-RO" Română (România)<br>
"sk-SK" Slovenčina (Slovensko)<br>
"sl-SI" Slovenščina (Slovenija)<br>
"fi-FI" Suomi (Suomi)<br>
"sv-SE" Svenska (Sverige)<br>
"vi-VN" Tiếng Việt (Việt Nam)<br>
"tr-TR" Türkçe (Türkiye)<br>
"el-GR" Ελληνικά (Ελλάδα)<br>
"bg-BG" Български (България)<br>
"ru-RU" Русский (Россия)<br>
"sr-RS" Српски (Србија)<br>
"uk-UA" Українська (Україна)<br>
"he-IL" עברית (ישראל)<br>
"ar-IL" العربية (إسرائيل)<br>
"ar-JO" العربية (الأردن)<br>
"ar-AE" العربية (الإمارات)<br>
"ar-BH" العربية (البحرين)<br>
"ar-DZ" العربية (الجزائر)<br>
"ar-SA" العربية (السعودية)<br>
"ar-IQ" العربية (العراق)<br>
"ar-KW" العربية (الكويت)<br>
"ar-MA" العربية (المغرب)<br>
"ar-TN" العربية (تونس)<br>
"ar-OM" العربية (عُمان)<br>
"ar-PS" العربية (فلسطين)<br>
"ar-QA" العربية (قطر)<br>
"ar-LB" العربية (لبنان)<br>
"ar-EG" العربية (مصر)<br>
"fa-IR" فارسی (ایران)<br>
"hi-IN" हिन्दी (भारत)<br>
"th-TH" ไทย (ประเทศไทย)<br>
"ko-KR" 한국어 (대한민국)<br>
"cmn-Hant-TW" 國語 (台灣)<br>
"yue-Hant-HK" 廣東話 (香港)<br>
"ja-JP" 日本語（日本）<br>
"cmn-Hans-HK" 普通話 (香港)<br>
"cmn-Hans-CN" 普通话 (中国大陆)<br>

## Security Considerations<br>
This script contacts Google servers in order send the recorded voice data and get back
the resulted text. The script uses TLS by default to encrypt all the traffic between
your PBX and Google servers so no 3rd party can eavesdrop your communication, but your
voice data will be available to Google under a not yet defined policy.

## Tiny version<br>
The '-tiny' suffixed scripts use the HTTP::Tiny perl module instead of LWP::UserAgent and
JSON::Tiny instead of JSON. This makes them a lot faster when run in small embedded systems
or boards like the Raspberry pi. They can be used as an in-place replacement of the normal
scripts and expose the same interface/cli args. To use them just make sure
you have HTTP::Tiny and JSON::Tiny installed.

## License<br>
The speech-recog script for asterisk is distributed under the GNU General Public
License v2. See COPYING for details.

## Homepage<br>
https://github.com/VitalPBX/Google_ASR_VitalPBX
