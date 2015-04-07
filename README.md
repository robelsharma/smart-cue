Eyeball MSSIPLibrary v10.0 Developer Reference Guide
---------------------------------------------------------------------------------------------------------------------------------
![](media/image1.png)


Last Modified: April 2015

Copyright © 2002-2014 Eyeball Networks Inc. Patented and patents pending. All rights reserved.


=========================================================================================================


1.  Eyeball MSSIPLibrary v10.0 Developer Reference Guide

    1.  [Introduction](#introduction)

        1.  [Supported Platforms and Features](#supported-platforms-and-features)

        2.  [Programming Conventions](#programming-conventions)

    2.  [Supported Standards](#supported-standards)

    3.  [Using Eyeball MS-SIP Library](#using-eyeball-ms-sip-library)

        1.  [Creating the Main Objects](#_bookmark6)

        2.  [Using the Features and Functions](#_bookmark7)

        3.  [Handling Event Notifications](#_bookmark8)

    4.  [Eyeball MS-SIP: APIs](#_bookmark9)

        1.  [MS-SIP Methods](#_bookmark10)

        2.  [MS-SIP Notification Events](#_bookmark11)

    5.  [Legal and Contact Information](#_bookmark12)


<span id="1._Introduction" class="anchor"><span id="_bookmark1" class="anchor"></span></span>Introduction
=========================================================================================================

MS-SIP library provides tools to application developers that allow integration of live audio, video communication with Lync servers and clients into new or existing applications and services.

![](media/image2.jpeg)


<span id="1.1._Supported_Platforms_and_Features" class="anchor"><span id="_bookmark2" class="anchor"></span></span>Supported Platforms and Features
---------------------------------------------------------------------------------------------------------------------------------------------------

MS-SIP library is written in C++ and available for Linux platform.

### Developer Platforms

```
Programming Languages: C++
Developer Tools: GCC
```
### Supported Features

> MS-SIP library supports the following features:

* Lync interoperability
* Multi-user login
* Multi-user calls
* Certificate verification for TLS connection Audio/Video
* Application share Instant Message (IM)
* Trusted Application support
* Lync federation call support
* SIP compression (MS-SIPCOMP)
* Bandwidth Management (MS-ICE2BWM)
* Presence (MS-PRES)
* HD video negotiation, BW estimation, PLI, VSR and more(MS-RTP,
MS-RTCP) 
* Microsoft standard encryption for Multiple call (MS-SRTP)
* Conncection Keep-Alive and MTLS (MS-CONMGMT) 
* NTLMv2 mechanism(MS-NLMP)
* Call Hold/Resume, BW extension, MSI, Video receive capabilities(MS-SDPEXT)
* Video Source Request for changing media codec on realtime
* End-point Identification (MS-SIPAE)
* Registration, provisioning mechanism (MS-SIPREGE) Header extension,GRUU(MS-SIPRE)
* Short-term credential mechanism for Media-Relay (MS-AVEDGEA)

<span id="1.2._Programming_Conventions" class="anchor"><span id="_bookmark3" class="anchor"></span></span>Programming Conventions
---------------------------------------------------------------------------------------------------------------------------------

### Programming Conventions

> In this document, the phrase current user refers to the local user, as opposed to the remote user.

We use the following conventions to define a variable’s data type:

* Variable name starting with ‘n’ and ‘i’ such as *nFileId, iField* refers to an Integer data type
* Variable name starting with ‘s’ such as sUserID refers to a String data type
* Variable name starting with ‘b’ such as *bAccept* refers to a Boolean data type
* Variable name starting with ‘a’ such as aContactList refers to an array data type. This is a one-dimensional array. Storing two-dimensional information is simply done by concatenating rows to each other, forming a one-dimensional array.

> All strings are case-sensitive unless specified otherwise.

<span id="2._Supported_Standards" class="anchor"><span id="_bookmark4" class="anchor"></span></span>Supported Standards
=======================================================================================================================

> The supported standard RFC are as follows: 


* MS-AVEDGEA
* MS-CONMGMT
* MS-ICE
* MS-ICE2
* MS-ICE2BWM 
* MS-NLMP
* MS-PRES
* MS-RTCP
* MS-SDPEXT
* MS-SIPAE
* MS-SIPCOMP
* MS-SIPRE
* MS-SIPREGE
* MS-SRTP
* MS-TURN
* MS-TURNBWM


<span id="3._Using_Eyeball_MS-SIP_Library" class="anchor"><span id="_bookmark5" class="anchor"></span></span>Using Eyeball MS-SIP Library
=========================================================================================================================================


1.  <span id="3.1._Creating_the_Main_Objects" class="anchor"><span id="_bookmark6" class="anchor"></span></span>Creating the Main Objects
-------------------------------------------------------------------------------------------------------------------------------------

```
class SimpleApp : public IEventHandler, public IAudioDataHandlerCallback, IVideoDataHandlerCallback
{
public:
MsSipAgent *agent;

SimpleApp(){
agent = new MsSipAgent(this);
}
}

SimpleApp *app = new SimpleApp();
```

2.  <span id="3.2._Using_the_Features_and_Functions" class="anchor"><span id="_bookmark7" class="anchor"></span></span>Using the Features and Functions
-------------------------------------------------------------------------------------------------------------------------------------


**With Lync server sign-up**

**Set TURN and SIP servers**

```
app->agent->SetTurnServerConfiguration(sStunServer, nStunPort, sTurnServer, nTurnPort);
app->agent->SetSipServerConfiguration(nAccountId, sSipServer, nSipPort, sDomain);
```

**Login**
```
app->agent->Login(nAccountId, sUsername, sPassword);
```

**OR**

**Without Lync server sign-up (Using MsSipLibrary as Trusted application)**

**Set Trusted application certificate and add default application endpoint**

```
app->agent->SetTrustedAppDomainCertificate(iApplicationAccount, sCertificatePath);
app->agent->AddTrustedApplicationEndpoint(iAccount, iApplicationAccount, sApplicationEndpoint, isDefault);
```

**Start Trusted application negotiation and create connection**

```
app->agent->StartTrustedApplicationNegotiation(iApplicationAccount, sApplicationId, iListeningPort, sApplicationGRUU, sSipProxyAddress, nSipPort, sApplicationDomain);
```

**Call**

```
app->agent->Call(nAccountId, sCallee, bConf, sConversationId);
```

3.  <span id="3.3._Handling_Event_Notifications" class="anchor"><span id="_bookmark8" class="anchor"></span></span>Handling Event Notifications
-------------------------------------------------------------------------------------------------------------------------------------

The application sends several event notifications to the application running on the MS-SIP library. The application needs to handle these events as necessary.

4. <span id="4._Eyeball_MS-SIP:_APIs" class="anchor"><span id="_bookmark9" class="anchor"></span></span>Eyeball MS-SIP: APIs
=========================================================================================================================

The Library
-----------

> The library uses the concept of *line* as follows:

* An application program may be a single-line application or a multi-line application.

* Each line is identified using a number. For example, if an application has 3 lines, the lines will be denoted as lines 0, 1 and 2.

* When an incoming call is received, Eyeball Messenger SDK assigns the first available line to the call. If all lines are busy, the caller will receive “Busy Here” and the call will not be established.

* The SIP Communicator control uses the concept of *multiple SIP accounts*:

* An application program may register multiple user accounts with multiple SIP proxy servers. Each SIP account is identified using a number provided by the application programmer.

* A SIP account could have multiple call lines, but not the reverse. A call line already used by one SIP account cannot be re-used by another SIP account.

* Methods and events of the library are described in the next section.


4.1 <span id="4.1_MS-SIP_Methods" class="anchor"><span id="_bookmark10" class="anchor"></span></span>MS-SIP Methods
---------------------------------------------------------------------------------------------------------------

### Methods

**4.1.1 Setting up the Library**
---------------------------------------------------------------------------------------------------------------

> *Use the following APIs for setting up TURN and SIP-Proxy.
> MsSipLibrary exposes two APIs for using predefined IP and port for
> media and signaling. Microsoft standard MTLS certificate verification
> and SIP message compression algorithm can be accessible too.*

#### SetTurnServerConfiguration (sTurnUdpAddress, iTurnUdpPort, sTurnTcpAddress, iTurnTcpPort)

> Set TURN server configuration

| Variable | Description |
| ------------- | ------------- |
| sTurnUdpAddress | TURN UDP server address  |
| iTurnUdpPort | TURN UDP server port  |
| sTurnTcpAddress | TURN TCP server address |
| iTurnTcpPort | TURN TCP server port |

#### SetSipServerConfiguration (iAccount, sSipProxyAddress, iSipPort, sSipDomain)

> Set SIP server configuration.

iAccount

The account number. sSipProxyA ddress:

SIP server address.

iSipPort:

SIP server port.

sSipDomain

:

SIP domain.

#### SetInterface(bMedia, &vsIPAddress)

Set the network interface(s) for media and signaling.

> *bMedia*:

Is true for media candidates, false for signaling.

vsIPAddres s:

Is the list of IP addresses.

#### SetPortRange(bMedia, uLower, uUpper)

Set the port ranges for media and signaling.

> *bMedia*:

Is true for media candidates, false for signaling.

> *uLower*:

Is the minimum port value.

> *uUpper*:

Is the maximum port value.

#### SetCertificateVerificationCallback( callback)

Set the callback function which will be invoked for verification of
certificate for TLS connection. Return 1 if the verification is to be
succeeded, 0 if the verification fails and the connection is to be
teared down

callback:

> the callback function.

#### StartSipCompressionNegotiation( iAccount)

> Start negotiation if SIP compression is to be enabled.
>
> iAccount:
>
> The account number.

### Standard Lync Sign in

> *After setting the SIP-Proxy address of Lync Edge or Front end the
> following APIs should be used for sign in. After using these APIs
> MsSipLibrary will work as a Lync Client.*

#### Login (iAccount, sUsername, sPassword)

> Login with a username and password.
>
> iAccount:
>
> The account number.
>
> sUsername:
>
> Username.
>
> sPassword:
>
> Password.

#### Logout(iAccount)

> Logout from an account.
>
> iAccount:
>
> The account number.

### Configure Library to work without signing in

> *MsSipLibrary can receive and make call and initiate an IM session
> without sign-in . To enable this feature make MsSipLibrary a Trusted
> App in Lync Front-End topology and create a static route for the
> trusted app listening port for receiving call and create an
> application endpoint for that trusted application for making outbound
> call.*

#### SetTrustedAppDomainCertificate(iApplicationAccount, &sCertificatePath)

> Set the AD signed domain certificate for trusted application domain.
> Certificate must be in **.pem** format.

iApplicati onAccount:

Is the trusted application account number.

sCertifica tePath:

Is the path of the AD signed domain certificate.

#### AddTrustedApplicationEndpoint(iAccount, iApplicationAccount, &sApplicationEndpoint, isDefault)

Add the default and other additional endpoint to the related trusted
application account.

iAcccount:

The account number provided by application (must be \>= 0).

iApplicati onAccount:

Is the trusted application account number.

sApplicati onEndpoint

:

SIP URI of the trusted application endpoint.

isDefault:

is true if the endpoint is default, otherwise false .

#### StartTrustedApplicationNegotiation(iApplicationAccount, &sApplicationId, iListeningPort,

> ***&sApplicationGRUU, &sSipProxyAddress, iSipPort,
> &sApplicationDomain)***

Start trusted application negotiation with Lync server for provisioning.

iApplicati onAccount:

Is the trusted application account number.

sApplicati onId:

The name of the Trusted Applications created on Lync Server

iListening Port:

The port on which trusted application will listen.

sApplicati onGRUU:

The service GRUU of trusted application created on Lync server.

sSipProxyA ddress:

Lync SIP server address

iSipPort:

SIP server port sApplicati onDomain:

> Domain of the created trusted application.

#### RemoveTrustedApplication(iApplicationAccount)

> Remove trusted application with all of its endpoint.
>
> iApplicati onAccount:
>
> Is the trusted application account number.

### Set and Use presence services

> *Lync uses extended SIP messages for presence. By using following APIs
> this service is achievable from the MSSIP library.*

#### SetPresence(iAccount, &sAvailability)

> Set presence for logged in user.
>
> iAccount:
>
> The account number.
>
> sAvailabil ity:
>
> Is the availability string such as - "online", "offline", "idle",
> "busy", "inacall", "busyidle", "donotdisturb" or "away".

#### Subscribe(iAccount, &sSubscribeURI)

> Subscribe for state category for the given remote user.
>
> iAccount:
>
> The account number.
>
> sSubscribe URI:
>
> URI of the remote user

#### UnSubscribe(iAccount, &sUnSubscribeURI)

> Un-subscribe for state category for the given remote user.
>
> iAccount:
>
> The account number.
>
> sSubscribe URI:
>
> URI of the remote user

#### GetSubscriberList(iAccount, \*pvList)

> Get the list of subscribers for the logged in user
>
> iAccount:
>
> The account number.
>
> pvList:
>
> The vector in which the list of subscribers' URIs will be inserted.

#### GetContactList(iAccount, \*pvList)

> Get the list of contacts for the logged in user.
>
> iAccount:
>
> The account number.
>
> pvList:
>
> The vector in which the list of subscribers' URIs will be inserted.

#### GetContactState(iAccount, &vContactURI, \*pvList)

> Get the state of remote user.
>
> iAccount:
>
> The account number.
>
> vContactUR I:
>
> URIs of contacts.
>
> pvList:
>
> states of the contacts will be inserted.

#### DirectorySearch(iAccount, sSearchName)

> Search for username in a domain containing desired name.
>
> iAccount:
>
> The account number.
>
> sSearchNam e:
>
> Is the name to be searched.

### Set codec for Media and use SRTP

> *Codec for audio, video and application share can be set by the
> following APIs. Microsoft uses their own media encryption protocol
> named*
>
> *MS-SRTP. This encryption feature is also available in MsSipLibrary.
> The expected video resolution, framerate, bitrate can also be
> published to the remote end through the library.*

#### SetAudioCodec(iLine, bModality, ullSSRC, &sAudioCodec)

> Set audio codecs.
>
> iLine:
>
> Line number for which the audio codec will be set.
>
> bModality:
>
> unused

ullSSRC:

Is the SSRC value that would be used for audio channel

sAudioCode c:

comma separated audio codecs each of whose attributes are separated by
space as the following format (the optional fourth attribute is for
fmtp):

"sCodecPayload1 sCodecName1 sCodecBitrate1 [sFmtpAttr1], sCodecPayload2
sCodecName2 sCodecBitrate2 [sFmtpAttr2]" eg., "0 PCMU 8000, 111 SIREN
16000, 101 telephone-event 8000 0-16"

#### SetVideoCodec (iLine, bModality, ullSSRC, &sVideoCodec)

Set video codecs.

iLine:

Line number for which the video codec will be set.

bModality:

should be true if attributes for video modality is to be present in the
SDP, false otherwise.

ullSSRC:

is the SSRC value that would be used for video channel

sVideoCode c:

comma separated video codecs each of whose attributes are separated by
space as the following format (the optional fourth attribute is for
fmtp)

"sCodecPayload1 sCodecName1 sCodecBitrate1 [sFmtpAttr1], sCodecPayload2
sCodecName2 sCodecBitrate2 [sFmtpAttr2]" eg., "34 H263 90000, 121
x-rtvc1 90000, 122 X-H264UC 90000 packetization-mode=1;mst-mode=NI-TC"

#### SetAppShare(iLine, &sCodec)

Set application sharing support.

iLine:

The line number in which the application share will be made.

sCodec:

The codec for the application sharing.

#### SetVideoReceiveCapabilities (iLine, sVideoPayloadType, sCapabilityId, sWidthofVideoFrame,

> ***sHeightofVideoFrame, sFramesPerSecond, sMaximumBitrate)***
>
> Set video receive capabilities.
>
> This function must be called immediately after setting video codec
> using SetVideoCodec(). For multiple resolutions this function must be
> called for each of them.
>
> To clear a resolution the sWidthofVideoFrame should be empty for that
> particular sVideoPayloadType and sCapabilityId.
>
> Since this setting is preserved, this function should be called
> (multiple times if necessary) before making or answering a call to
> set/reset the resolution capabilities.
>
> This API can be used to set capabilities through "a=fmtp:". Provide
> empty string in the last three parameters and capability value for
> sWidthofVideoFrame. eg., SetVideoReceiveCapabilities(0, "121", "263",
> "CIF=15", "", "", "");.
>
> For multiple capability value this function must be called for each of
> them. Before making a call the iLine should be -1.
>
> iLine:
>
> The line number for which the video capabilities will be set.
>
> sVideoPayl oadType:
>
> The payload Number.
>
> sCapabilit yId:
>
> The random unique Id. sWidthofVi deoFrame:
>
> The video frame width. sHeightofV ideoFrame:
>
> The video frame height. sFramesPer Second:
>
> The video frame per second.
>
> sMaximumBi trate:
>
> The maximum bitrate per second.

#### EnableSrtp ( iSrtpState)

> Set SRTP mode to Rejected, Optional or Required
>
> iSrtpState
>
> :
>
> 0(Rejected), 1(Optional) and 2(Required)

### Instant Messaging

> *Microsofts' session initiated Instant messaging is available in
> MsSipLibrary. This feature can be accessible through the following
> APIs.*

#### SetIM(iLine, &supportedIMTypes)

> Set supported text message types.
>
> iLine:
>
> The line number for which the message types will be set.
>
> supportedI MTypes:
>
> Space separated message types as the following format "sMessageType1
> sMessageType2" eg., "text/plain text/rtf text/html"

#### SendChatMessage(iLine, &sMessage)

> Send text chat message.
>
> iLine:
>
> The line number in which the text message will be sent.
>
> sMessage:
>
> The text message that will be sent

### Make and Receive Call

> The following APIs are common for audio, video, application share and
> chat session. After a successful call MsSipLibrary provides parsed raw
> media data as RTP header and payload. Inside of the library the
> encrypted media data is decrypted and provided as raw payload.

#### Call (iAccount, sCallee, bConf, &sConversationId)

> Initiate call. Returns the line number that has been used to make the
> call
>
> iAccount:
>
> The account number from which the call is to be made.
>
> sCallee:
>
> Callee URI.
>
> bConf:
>
> True if the call is to be added to a conference call, false if the
> call is to be one-one.
>
> sConversat ionId:
>
> The conversation ID.
>
> This ID can be used to track if the new dialog is under an existing
> conversation or create a new conversation and also to create different
> dialogs with different media but under the same conversation.

#### HoldLine(iLine, bHold)

> Hold or resume a running call.
>
> iLine:
>
> The line on which the call is going on.
>
> bHold:
>
> hold or unhold. (true for hold, false for unhold).

#### EndCall (iLine, bEndAll)

> End a running call.
>
> iLine:
>
> The line on which the call is going on, -1 to end all calls
>
> bEndAll:
>
> True if the call is ended otherwise false.

#### ModifySession (iLine, bUnused, bVideo)

> Add/remove video in/from an on going call.
>
> iLine:
>
> The line on which the call is going on.
>
> bUnused:
>
> Reserved.
>
> bVideo:
>
> True to add video, false to remove video.

#### RespondCall ( iLine, bAccept, bConf, iReasonCode, &sReasonPhrase)

> Respond an incoming call request.
>
> For accepting the call the iReasonCode must be 0 or sReasonPhrase
> empty string. For declining a custom iReasonCode with sReasonPhrase
> can be given, otherwise 0 as the reason code or empty string as the
> reason phrase should be given.
>
> iLine:
>
> The line on which the call is going on.
>
> bAccept:
>
> True to accept, false to reject.
>
> bConf:
>
> True if the callee accepts to join a conference, false if the call is
> to be one-one.
>
> iReasonCod e:
>
> The reason code for declining the call (ex: 415).
>
> sReasonPhr ase:
>
> The reason for declining the call (ex: Unsupported Media Type)

#### RespondReinvite ( iLine)

> Respond upon an incoming reinvite request.
>
> This function must be called upon receiving OnReinviteRequest event.
>
> iLine:
>
> The line on which the call is going on.

#### SendSipRequestInCall(iLine, &msgType, &contentType, &content)

Send SIP request in dialog.

iLine:

The line on which the call is going on.

msgType:

The message which can be INFO or MESSAGE.

contentTyp e:

List of content types that need to be set for each body.

content:

The list of bodies.

#### SendSipResponse(iLine, &sRequest, &sResponseCode, &sResponseBody)

Send SIP response in dialog.

iLine:

The line on which the call is going on.

sRequest:

The request for which response is to be sent.

sResponseC ode:

The response code. sResponseB ody:

The response body.

#### GetSipHeader ( &sMsg, &sHeaderName, sValue)

Parse the desired sip header from sip message.returns the number of
values extracted

sMsg:

The sip message from which the header will be extracted.

sHeaderNam e:

The name of the sip header whose value is needed to be extracted.

sValue:

A vector of string containing the desired header's values.

#### AddSipHeader (&sMsg, &sHeaderName, &sValue)

Add the desired sip header to a sip message.

sMsg:

The sip message from which the header will be added.

sHeaderNam e:

The name of the sip header whose value is needed to be added.

sValue:

A vector of string containing the desired header's values.

#### PutCallInConference ( iLine)

Put an ongoing call into a conference.

iLine:

The line on which the call is going on.

#### SendAudioData( iLine, pRtpHeader, iRtpHeaderLength, pData, iDataLength)

Send audio data.

iLine:

The line on which the call is going on.

PRtpHeader

:

The RTP header. iRtpHeader Length:

Length of the RTP header.

pData:

Data iDataLengt h:

The length of data

#### SendVideoData( iLine, pRtpHeader, iRtpHeaderLength, pData, iDataLength)

Send video data.

iLine:

The line on which the call is going on.

PRtpHeader

:

The RTP header. iRtpHeader Length:

Length of the RTP header.

pData:

Data iDataLengt h:

The length of data

#### SendApplicationData( iLine, pRtpHeader, iRtpHeaderLength, pData, iDataLength)

Send application data in application sharing call.

iLine:

The line on which the call is going on.

PRtpHeader

:

The RTP header. iRtpHeader Length:

Length of the RTP header.

pData:

Data

> iDataLengt h:
>
> The length of data

#### SetAudioCallback ( iLine, pHandler)

> Set callback for audio data.
>
> iLine:
>
> The line on which the call is going on.
>
> pHandler:
>
> HandleAudioCallback method is fired when audio data is available,
> application must implement this method to handle received audio data.

#### SetVideoCallback ( iLine, pHandler)

> Set callback for video data.
>
> iLine:
>
> The line on which the call is going on.
>
> pHandler:
>
> HandleVideoCallback method is fired when video data is available,
> application must implement this method to handle received video data.

### Eyeball's Bandwidth estimation and Lync standard bandwidth management

> MsSipLibrary uses the Microsoft specified bandwidth estimation
> technique and also integrated an Eyeball's statistical bandwidth
> estimation method with it, which ensures better voice and video
> quality.

#### EnableBandwidthManagement ( bEnableBWM)

> Enable or disable bandwidth management.
>
> bEnableBWM
>
> :
>
> True to enable, false to disable.

#### SetBandwidthForMedia (iMedia, iMin, iMax)

> Set the bandwidth for a media channel.
>
> iMedia:
>
> 0 for audio, 1 for video, 2 for appshare.
>
> iMin:
>
> Minimum bandwidth.
>
> iMax:
>
> Maximum bandwidth.

#### StartBandwidthEstimationForRemoteUser ( iLine)

> Start RTCP packet train for bandwidth estimation between the pair.
>
> iLine:
>
> The line on which the call is going on.

#### SendModalitySendBandwidthLimit (iLine, iBandwidthLimit)

> Send Modality Send Bandwidth Limit in RTCP
>
> iLine:
>
> The line on which the call is going on.
>
> iBandwidth Limit:
>
> The maximum outbound bandwidth in bits per second available for the
> specified modality type.

#### SendPolicyServerBandwidthProfile (iLine, iMedia, iBandwidthPolicyProfile)

> Send Policy server bandwidth policy profile for the media in RTCP.
>
> iLine:
>
> The line on which the call is going on.
>
> iMedia:
>
> 0 for audio, 1 for video, 2 for appshare.
>
> iBandwidth PolicyProf ile:
>
> The max bandwidth supported by policy server for this media.

#### SendTURNServerSupportedBandwidth ( iLine, iMedia, iMaxBandwidth)

> Send TURN server max supported bandwidth for the media in RTCP.
>
> iLine:
>
> The line on which the call is going on.
>
> iMedia:
>
> 0 for audio, 1 for video, 2 for appshare.
>
> iMaxBandwi dth:
>
> The max bandwidth supported by TURN for this media.

### HD video negotiation and other microsoft profile specific extension feature

> In an ongoing call the video resolution of a remote peer can be
> changed through the MsSipLibrary. Microsoft's other profile specific
> extension like picture loss indication, packet loss notification,
> network congestion, video source request, audio healer matrix, etc are
> also available and accessible through the following APIs.

#### SendVideoResolutionPreference (iLine, iWidth, iHeight, iBitRate, iFrameRate)

Send preferred video resolution to remote client for a video call in
RTCP.

iLine:

The line on which the call is going on.

iWIdth:

The preferred width in pixel for video.

iHeight:

The preferred height in pixel for video.

iBitRare:

Unused.

iFrameRate

:

Unused.

#### SendPacketLossNotification ( iLine, iPacketNumber)

Send packet loss notification to remote client if the packet loss
occurred in RTCP.

iLine:

The line on which the call is going on.

iPacketNum ber:

The sequence number of the packet.

#### SendNetworkCongestionNotification ( iLine)

Send network congestion information to remote client in RTCP

iLine:

The line on which the call is going on.

#### SendRTCPVideoSourceRequest( iLine, iPayloadType, iUCConfigMode, iFlags, iAspectRatioBitMask, iM aximumWidth, iMaximumHeight, uiMinimumBitRate, uiReserve, uiBitratePerLevel, usiaBitRateHistogra m, uiFrameRateBitMask, iNumberofMUSTInstances, iNumberofMAYInstances, usiaQualityReportHisto gram, uiMaximumNumberofPixels)

Send RTCP Video Source Request in RTCP

iLine:

The line on which the call is going on.

iPayloadTy pe:

Is the media Payload Type

iUCConfigM ode:

Is the UC configuration Mode

iFlags:

Flags for VSR . iAspectRat ioBitMask:

Is the bit mask of aspect ratio of video source.

iMaximumWi dth:

Maximum width of video.

iMaximumHe ight:

Maximum height of video

uiMinimumB itRate:

Minimum bitrate of video

uiReserve:

Reserve uiBitrateP erLevel:

Bitrate per level for video usiaBitRat eHistogram

:

pointer of BitRateHistogram array of length 10.

uiFrameRat eBitMask:

Bit mask of frame rate. iNumberofM USTInstanc

es:

Number of MUST instances of video source.

iNumberofM AYInstance s:

Number of MAY instances of video source.

usiaQualit yReportHis togram:

Quality report histogram array of length 8.

uiMaximumN umberofPix els:

Vector of maximum number of pixels.

#### SendRTCPGoodBye( iLine, iMedia)

Send RTCP Goodbye

iLine:

The line on which the call is going on.

iMedia:

0 for audio, 1 for video, 2 for appshare.

#### SendDominantSpeaker ( iLine)

Send dominant speaker in RTCP.

iLine:

The line on which the call is going on.

#### SendDominantSpeakerHistory( iLine)

Send dominant speaker history in RTCP.

iLine:

The line on which the call is going on.

#### SendReceiverReport( iLine)

Send Receiver Report for a Line with RTCP.

iLine:

The line on which the call is going on.

#### SendPictureLossIndication (iLine, iRequestId, iSFR[])

Send picture loss indication.

iLine:

The line on which the call is going on.

iRequestId

:

Uniquely identifies the PLI.

iSFR[]:

The integer array list of Sync frame request of length.

#### SendAudioHealerMetrics(iLine, iConcealedFrames, iStretchedFrames, iCompressedFrames, iTotalFrames, iReceiveQualityState, iFECdistanceRequest)

Send audio healer metrics.

iLine:

The line number for which the video capabilities will be set.

iConcealed Frames:

The total number of concealed audio frames that have been generated
during the call.

iStretched Frames:

The total number of stretched frames that have been generated during the
call.

iCompresse dFrames:

The total number of compressed frames that have been generated during
the call.

iTotalFram es:

The total number of frames that have been generated during the call.

iReceiveQu alityState

:

The received audio quality so far in the call.

iFECdistan ceRequest:

The the FEC distance requested by the receiver from the sender.

> ![](media/image2.jpeg)
>
> *Copyright © 2002-2014 Eyeball Networks Inc. Patented and patents
> pending. All rights reserved.*

<span id="4.2_MS-SIP_Notification_Events" class="anchor"><span id="_bookmark11" class="anchor"></span></span>4.2 MS-SIP Notification Events
-------------------------------------------------------------------------------------------------------------------------------------------

### Notification Events

> The following notification events are supported by the SIP
> Communicator.

#### OnSipCompressionNegotiationResponse(iAccount, bSuccess)

> Fired upon reception of response for SIP compression negotiation
> request.
>
> *iAccount*:
>
> The account number.
>
> *bSuccess*:
>
> True if SIP compression has been enabled successfully, false otherwise

#### OnLoginResponse(eLoginResponse, iAccount)

> Fired upon login response.
>
> eLoginResp onse:
>
> ELoginResponseSuccess, ELoginResponseFailure, ELoginResponseTimeout,
> ELoginResponseAuthenticationError .
>
> iAccount:
>
> The account number.

#### OnAddTrustedApplicationEndpointComplete(iApplicationAccount, iAccount, iSuccess, isDefault)

> Fired upon negotiation completion of trusted application endpoint.
>
> iApplicati onAccount:
>
> The account of the trusted application.
>
> iAccount:
>
> The account number of the trusted application endpoint.
>
> iSuccess:
>
> Status of new endpoint addition.
>
> isDefault:
>
> True if this is an default endpoint.

#### OnTrustedApplicationConnectionLost(iApplicationAccount)

> Fired when connection of trusted application listening port becomes
> INVALID.
>
> *iApplicati onAccount*:
>
> The application account number of trusted application.

#### OnTrustedApplicationRemoveComplete(iApplicationAccount, iSuccess)

> Fired when connection of trusted application listening port becomes
> INVALID.
>
> *iApplicati onAccount*:
>
> The application account number of trusted application.
>
> *iSuccess*:
>
> Status of the application remove operation.

#### OnIncomingCall(iLine, &sCaller, bVideo, bAppShare, bIM, bConf, iAccount, iApplicationAccount,

> ***&sMsg, &sConversationId, isDefaultRouted)***
>
> Fired upon incoming call
>
> *iLine:*
>
> The line at which the call request has been received.
>
> *sCaller:*
>
> Caller uri.
>
> *bVideo:*
>
> True for audio-video call, false for audio only call.
>
> *bAppShare:*
>
> True for application share call.
>
> *bIM:*
>
> True for chat session call.
>
> *bConf:*
>
> True if the call is to be put into a conference, false for 1-1 call
>
> *iAccount:*
>
> The account number. *iApplicati onAccount:*
>
> The account of the trusted application.
>
> *sMsg:*
>
> The SIP message of the incoming call request.
>
> *sConversat ionId:*
>
> The conversation id. *isDefaultR outed:*
>
> TRUE if default route used.

#### OnSendingCallRequest(iLine, &sMsg)

> Fired when INVITE message has been created and can be edited by the
> client application before it is sent.
>
> *iLine:*
>
> The line that will be used for the call.
>
> *sMsg:*
>
> The SIP message of the outgoing call request.

#### OnReinviteRequest(iLine, bVideo, &sMsg)

Fired upon reinvite request

> *iLine:*

The line on which the call is going on.

> *bVideo:*

True if remote user want to add video, false if he wants to remove
video.

> *sMsg:*

The SIP message of the reinvite request.

#### OnReinviteResponse(iLine, bVideo)

Fired upon response to reinvite request

> *iLine:*

The line on which the call is going on.

> *bVideo:*

True if remote user want to add video, false if he wants to remove
video.

#### OnEndCall(iLine)

Fired upon call ending.

> *iLine:*

The line on which call has been ended.

#### OnCallResponse(iLine, bResponse, bVideo, bAppShare, bIM, bConf, &sMsg)

Fired upon response to a call request.

> *iLine:*

The line on which the call is going on.

> *bResponse:*

True for success, false for failure.

> *bVideo:*

True for video, false for audio only call.

> *bAppShare:*

True for application share call.

> *bIM:*

True for chat session call.

> *bConf:*

True if callee wants to be put into conference, false for 1-1 call.

> *sMsg:*

Response for invite message

#### OnSDPAvailable(iLine, &sSDP)

Fired when SDP has been created and can be edited by the client
application if required before sending the SIP message.

> *iLine:*

The line on which the call is going on.

> *sSDP:*

The SDP which can be modified by the client application.

#### OnReceivedSipMessage(iAccount, iLine, &sMsg, \*bHandled)

Fired when a SIP message has been received. This can be edited by the
client application if required before it is processed by the library.

> *iAccount:*

The account number of the received SIP message.

> *iLine:*

The line in which the call is going on, or -1 if the message isn’t
related to a call dialog.

> *sMsg:*

The SIP message

> *bHandled:*

Set to TRUE if MSLib should not parse and handle the SIP message, or
FALSE if MSLib should parse and handle the SIP message.

#### OnSendingSipMessage(iAccount, iLine, &sMsg)

Fired when a SIP message has been created and can be edited by the
client application if required before it is sent.

> *iAccount:*

The account number.

> *iLine:*

The line on which the call is going on.

> *sMsg:*

The SIP message which can be modified by the client application.

#### OnLogoutComplete( iAccount)

Fired upon logout process completion.

> *iAccount:*

The Account number.

#### OnConnectionLost( iAccount)

Fired upon connection to SIP server gets lost.

> *iAccount:*

The Account number.

#### OnReceivedReceiverReportForRemoteUser(iLine, uiSSRC, fractionLost, uiCumulativePacketsLost, uiExtendedHighestSequence, uiInterarrivalJitter, uiLastSR, uidelayLastSR)

Fired when receiver report is found.

> *iLine:*

The line on which the call is going on.

> *uiSSRC:*
>
> SSRC of source. *fractionLo st:*
>
> Fraction lost . *uiCumulati vePacketsL ost:*

Cumulative number of packets lost.

> *uiExtended HighestSeq uence:*

Extended highest sequence number received.

> *uiInterarr ivalJitter*
>
> *:*

Inter-arrival jitter.

> *uiLastSR:*

Last Sender Report (LSR).

> *uidelayLas tSR:*

Delay since last Sender Report (DLSR).

#### OnReceivedBandwidthEstimationForRemoteUser(iLine, iEstimatedBandwidth)

Fired when bandwidth estimation between the pair is found.

> *iLine:*

The line on which the call is going on.

> *iEstimated Bandwidth:*

Is the remote end estimated bandwidth for use

#### OnSendingBandwidthEstimationToRemoteUser(iLine, \*pulEstimatedBandwidth)

Fired when bandwidth estimation between the pair is computed and ready
to send.

> *iLine:*

The line on which the call is going on.

pulEstimat edBandwidt h:

Is the estimated bandwidth which can be modified by the application.

#### OnReceivedPacketLossNotification( iLine, iPacketNumber)

Fired upon packet loss notification received

> *iLine:*

The line on which the call is going on.

> *iPacketNum ber:*

Is the sequence number of the packet.

#### OnReceivedVideoResolutionPreference( iLine, iWidth, iHeight)

Fired upon reception of preferred video resolution of remote client for
a video.

> *iLine:*

The line on which the call is going on.

> *iWidth:*

Is the preffered width in pixel for video.

> *iHeight:*

The line on which the call is going on.

#### OnReceivedPolicyServerBandwidthProfile(iLine,iMedia, iBandwidthPolicyProfile)

Fired upon reception of Policy server's bandwidth policy profile.

> *iLine:*

The line on which the call is going on.

> *iMedia:*

0 for audio, 1 for video, 2 for appshare.

> *iBandwidth PolicyProf ile:*

Is the max bandwidth supported by policy server for this media.

#### OnReceivedTURNServerSupportedBandwidth(iLine,iMedia, iMaxBandwidth)

Fired upon reception of TURN server max supported bandwidth.

> *iLine:*

The line on which the call is going on.

> *iMedia:*

0 for audio, 1 for video, 2 for appshare.

> *iMaxBandwi dth:*

Is the max bandwidth supported by TURN for this media.

#### OnReceivedNetworkCongestionNotification(iLine, iCongestionState)

Fired upon reception of network congestion info.

> *iLine:*

The line on which the call is going on.

> *iCongestio nState:*

Is the network state.

#### OnReceivedModalitySendBandwidthLimit(iLine, iBandwidthLimit)

Fired upon reception of Modality Send Bandwidth Limit.

> *iLine:*

The line on which the call is going on.

> *iBandwidth Limit:*

the maximum outbound bandwidth in bits per second available for the
specified modality type.

#### OnRTCPVideoSourceRequest(iLine,

> ***iNumberofEntries, viPayloadType, viUCConfigMode, viFlags,
> viAspectRatioBitMask, viMaximumWidth, viMaximumHeight,
> vuiMinimumBitRate, vuiReserve,***
>
> ***vuiBitratePerLevel, vusiaBitRateHistogram, vuiFrameRateBitMask,
> viNumberofMUSTInstances, viNum berofMAYInstances,
> vusiaQualityReportHistogram, vuiMaximumNumberofPixels)***

Fired upon reception of RTCP Video Source Request.

> *iLine:*

The line on which the call is going on.

> *iNumberofE ntries:*
>
> Number of entries. *viPayloadT ype:*

Vector of PayloadType.

> *viUCConfig Mode:*

Vector of UCConfigMode.

> *viFlags:*
>
> Vector of Flags. *viAspectRa tioBitMask*
>
> *:*

Vector of AspectRatioBitMask.

> *viMaximumW idth:*

Vector of MaximumWidth.

> *viMaximumH eight:*

Vector of MaximumHeight.

> *vuiMinimum BitRate:*

Vector of MinimumBitRate.

> *vuiReserve*
>
> *:*
>
> Vector of Reserve. *vuiBitrate PerLevel:*
>
> Vector of BitratePerLevel. *vusiaBitRa teHistogra*
>
> *m:*

pointer vector of BitRateHistogram array.

> *vuiFrameRa teBitMask:*

Vector of FrameRateBitMask.

> *viNumberof MUSTInstan ces:*

Vector of NumberofMUSTInstances.

> *viNumberof MAYInstanc es:*

Vector of NumberofMAYInstances.

> *vusiaQuali tyReportHi stogram:*

Vector of QualityReportHistogram array.

> *vuiMaximum NumberofPi xels:*

Vector of MaximumNumberofPixels.

#### OnReceivedRTCPGoodByeRequest(iLine, iMediaType)

Fired upon reception of RTCP GoodBye request

> *iLine:*

The line on which the call is going on.

> *iMedia:*

Type of media. 0 for Audio , 1 for Video and 2 for appshare.

#### OnReceivedDominantSpeaker(iLine, &sUser)

Fired upon reception of dominant speakers.

> *iLine:*

The line on which the call is going on.

> *sUser:*

The dominant speaker.

#### OnReceivedDominantSpeakerHistory(iLine, sDominantSpeakerHist)

Fired upon reception of dominant speaker history.

> *iLine:*

The line on which the call is going on.

> *sDominantS peakerHist*
>
> *:*

Is the list of Dominant speaker Ascending order separated by space

#### OnReceivedPictureLossIndication(iLine, iRequestId, SFR[])

Fired upon reception of picture loss indication.

> *iLine:*

The line on which the call is going on.

> *iRequestId*
>
> *:*

That uniquely identifies the PLI

> *SFR[]:*

Is the integer array list of Sync frame request of length 8

#### OnReceivedAudioHealerMetrics(iLine, iConcealedFrames, iStretchedFrames, iCompressedFrames, iTotalFrames, iReceiveQualityState, iFECdistanceRequest)

Fired upon reception of audio healer metrics.

> *iLine:*

The line on which the info received

> *iConcealed Frames:*

Is the total number of concealed audio frames that have been generated
during the call.

> *iStretched Frames:*

Is the total number of stretched frames that .

> *iCompresse dFrames:*

The total number of compressed frames that have been generated during
the call.

> *iTotalFram es:*

Is the total number of frames that have been generated during the call.

> *iReceiveQu alityState*
>
> *:*

Is the received audio quality so far in the call.

> *iFECdistan ceRequest:*

Is the FEC distance requested by the receiver from the sender.

#### OnCategoryDataChanged( iAccount, &vUri, &vCategoryName, &vCategoryValue )

Fired upon change in remote buddies' category data.

> *iAccount:*

The account number.

> *vUri:*

List of sip Uri of whose category data changed

> *vCategoryN ame:*
>
> List of category name *vCategoryV alue:*

List of category value.

#### OnSubscriberAdded( iAccount, &vSubscriber)

Fired upon change in remote buddies' category data.

> *iAccount:*

The account number.

> *vSubscribe r:*

List of new subscriber URIs

#### OnUpdateContactList( iAccount)

Fired when the contact list in available for the logged in user

> *iAccount:*

The account number.

#### OnUpdateSubscriberList( iAccount)

Fired when the subscriber list (the remote users who have subscribed for
the logged in user) in available for the logged in user

> *iAccount:*

The account number.

#### OnReceivedChatMessage( iLine, &sContentType, &sMessage)

Fired upon incoming chat message received.

> *iLine:*

The line on which the chat message received.

> *sContentTy pe:*

The content type of received chat message.

> *sMessage:*

The content of the received chat message.

#### OnReceivedApplicationData(iLine, \*pRtpHeader, \*pData, iLen, iCSRCCount)

Fired upon incoming application data received in application share
session.

> *iLine:*

The line on which the share is going on.

> *pRtpHeader*
>
> *:*

the RTP header

> *pData:*

The data

> *iLen:*

Length of the data

> *iCSRCCount*
>
> *:*

Number of CSRC

#### OnUpdateCallStatus( iLine, bHold)

Fired when call is held/resumed

> *iLine:*

The line on which the call is going on.

> *bHold:*

True if call is held, false if it's resumed

#### HandleAudioCallback( iLine, \*pRtpHeader, \*pData, iLen, iCSRCCount)

Fired when data is received on the audio RTP channel

> *iLine:*
>
> The line on which the call is going on.
>
> *pRtpHeader*
>
> *:*
>
> the RTP header
>
> *pData:*
>
> The data
>
> *iLen:*
>
> Length of the data
>
> *iCSRCCount*
>
> *:*
>
> Number of CSRC

#### HandleVideoCallback( iLine, \*pRtpHeader, \*pData, iLen, iCSRCCount)

> Fired when data is received on the video RTP channel
>
> *iLine:*
>
> The line on which the call is going on.
>
> *pRtpHeader*
>
> *:*
>
> the RTP header
>
> *pData:*
>
> The data
>
> *iLen:*
>
> Length of the data
>
> *iCSRCCount*
>
> *:*
>
> Number of CSRC
>
> ![](media/image2.jpeg)
>
> *Copyright © 2002-2014 Eyeball Networks Inc. Patented and patents
> pending. All rights reserved.*

<span id="5.__Legal_and_Contact_Information" class="anchor"><span id="_bookmark12" class="anchor"></span></span>Legal and Contact Information
=============================================================================================================================================

> ![](media/image4.png)

Legal and Contact Information
-----------------------------

> Copyright © 2002-2014 Eyeball Networks Inc. Patented and patents
> pending. All rights reserved.

  > **Department**      > **E-mail**
  --------------------- -----------------------------
  > Sales               > <sales@eyeball.com>
  > Technical Support   > <techsupport@eyeball.com>

> **Corporate Headquarters:**
>
> 730 - 1201 West Pender Street Vancouver, BC V6E 2V2 Canada
>
> Tel. +1 604.921.5993
>
> Fax +1 604.921.5909
>
> ![](media/image2.jpeg)
>
> *Copyright © 2002-2014 Eyeball Networks Inc. Patented and patents
> pending. All rights reserved.*
