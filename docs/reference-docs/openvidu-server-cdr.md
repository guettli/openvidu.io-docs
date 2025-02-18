<h2 id="section-title">OpenVidu CDR</h2>
<hr>

OpenVidu Server offers a CDR logging system that informs about all the relevant events that take place in the system. To start OpenVidu Server with CDR enabled, set [configuration property `OPENVIDU_CDR=true`](reference-docs/openvidu-config/){:target="_blank"}. The CDR file location is given by configuration property `OPENVIDU_CDR_PATH`, default to `/opt/openvidu/cdr`.

The CDR file is a plain UTF-8 text file complying with [JSON Lines](http://jsonlines.org/){:target="_blank"} format: one standard JSON entry for each line. All JSON entries share the following structure:

```json
{"EVENT_NAME": {"timestamp": 1234567890, "PROP_1":"VAL_1", "PROP_2":"VAL_2", ... , "PROP_N":"VAL_N" }}
```

So every entry is a JSON object with a single key (the event name) and a JSON object as value (the event content). This JSON value follows this format:

- `timestamp`: a number with the time when the event was registered in UTC milliseconds.
- `PROP_1`, `PROP_2` ... `PROP_N` : custom properties for each specific event. Their name and type differ from each other.

### Events in OpenVidu CDR

- [**sessionCreated**](#sessioncreated)
- [**sessionDestroyed**](#sessiondestroyed)
- [**participantJoined**](#participantjoined)
- [**participantLeft**](#participantleft)
- [**webrtcConnectionCreated**](#webrtcconnectioncreated)
- [**webrtcConnectionDestroyed**](#webrtcconnectiondestroyed)
- [**recordingStatusChanged**](#recordingstatuschanged)
- [**filterEventDispatched**](#filtereventdispatched)
- [**signalSent**](#signalsent)
- [**mediaNodeStatusChanged**](#medianodestatuschanged)<a href="openvidu-pro/" target="_blank"><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a>
- [**autoscaling**](#autoscaling)<a href="openvidu-pro/" target="_blank"><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a>

<br>

---

#### sessionCreated

Recorded when a new session has been created.

| Property    | Description                               | Value                                       |
| ----------- | ----------------------------------------- | ------------------------------------------- |
| `sessionId` | Session for which the event was triggered | A string with the session unique identifier |
| `timestamp` | Time when the event was triggered         | UTC milliseconds                            |

```json
{
  "sessionCreated": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601394690713
  }
}
```

<br>

---

#### sessionDestroyed

Recorded when a session has finished.

| Property    | Description                               | Value                                                                                 |
| ----------- | ----------------------------------------- | ------------------------------------------------------------------------------------- |
| `sessionId` | Session for which the event was triggered | A string with the session unique identifier                                           |
| `timestamp` | Time when the event was triggered         | UTC milliseconds                                                                      |
| `startTime` | Time when the session started             | UTC milliseconds                                                                      |
| `duration`  | Total duration of the session             | Seconds                                                                               |
| `reason`    | Why the session was destroyed             | [`"lastParticipantLeft"`,<br>`"sessionClosedByServer"`,<br>`"openviduServerStopped"`] |

```json
{
  "sessionDestroyed": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601395365656,
    "startTime": 1601394690713,
    "duration": 674,
    "reason": "lastParticipantLeft"
  }
}
```

<br>

---

#### participantJoined

Recorded when a user has connected to a session.

| Property        | Description                                                                            | Value                                                   |
| --------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `sessionId`     | Session for which the event was triggered                                              | A string with the session unique identifier             |
| `timestamp`     | Time when the event was triggered                                                      | UTC milliseconds                                        |
| `connectionId` | Identifier of the participant                                                          | A string with the participant unique identifier         |
| `location`      | Geo location of the participant <a href="openvidu-pro/" target="_blank"><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a> | A string with format `"CITY, COUNTRY"` (or `"unknown"`) |
| `platform`      | Complete description of the platform used by the participant to connect to the session | A string with the platform description                  |
| `clientData`    | Metadata associated to this participant from the client side. This corresponds to parameter `metadata` of openvidu-browser method [`Session.connect`](api/openvidu-browser/classes/session.html#connect){:target="_blank"} | A string with the participant client-side metadata (generated when calling `Session.connect` method) |
| `serverData`    | Metadata associated to this participant from the server side. This corresponds to parameter `data` of REST API operation [POST /openvidu/api/sessions/&lt;SESSION_ID&gt;/connection](reference-docs/REST-API#post-openviduapisessionsltsession_idgtconnection){:target="_blank"} or its Java/Node server SDKs variants | A string with the participant server-side metadata |

```json
{
  "participantJoined": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601394715606,
    "connectionId": "con_EIeO06zgMz",
    "location": "Berlin, Germany",
    "platform": "Chrome 85.0.4183.121 on Linux 64-bit",
    "clientData": "Mike",
    "serverData": "{'user': 'client1'}"
  }
}
```

<br>

---

#### participantLeft

Recorded when a user has left a session.

| Property        | Description                                                                            | Value                                                                                                                                                                |
| --------------- | -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sessionId`     | Session for which the event was triggered                                              | A string with the session unique identifier                                                                                                                          |
| `timestamp`     | Time when the event was triggered                                                      | UTC milliseconds                                                                                                                                                     |
| `connectionId` | Identifier of the participant                                                          | A string with the participant unique identifier                                                                                                                      |
| `location`      | Geo location of the participant <a href="openvidu-pro/" target="_blank"><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a> | A string with format `"CITY, COUNTRY"` (or `"unknown"`)                                                                                                              |
| `platform`      | Complete description of the platform used by the participant to connect to the session | A string with the platform description                                                                                                                               |
| `clientData`    | Metadata associated to this participant from the client side. This corresponds to parameter `metadata` of openvidu-browser method [`Session.connect`](api/openvidu-browser/classes/session.html#connect){:target="_blank"} | A string with the participant client-side metadata (generated when calling `Session.connect` method) |
| `serverData`    | Metadata associated to this participant from the server side. This corresponds to parameter `data` of REST API operation [POST /openvidu/api/sessions/&lt;SESSION_ID&gt;/connection](reference-docs/REST-API#post-openviduapisessionsltsession_idgtconnection){:target="_blank"} or its Java/Node server SDKs variants | A string with the participant server-side metadata |
| `startTime`     | Time when the participant joined the session                                           | UTC milliseconds                                                                                                                                                     |
| `duration`      | Total duration of the participant's connection to the session                          | Seconds                                                                                                                                                              |
| `reason`        | How the participant left the session                                                   | [`"disconnect"`,<br>`"forceDisconnectByUser"`,<br>`"forceDisconnectByServer"`,<br>`"sessionClosedByServer"`,<br>`"networkDisconnect"`,<br>`"openviduServerStopped"`] |

```json
{
  "participantLeft": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601395365655,
    "startTime": 1601394715606,
    "duration": 650,
    "reason": "disconnect",
    "connectionId": "con_EIeO06zgMz",
    "location": "Berlin, Germany",
    "platform": "Chrome 85.0.4183.121 on Linux 64-bit",
    "clientData": "Mike",
    "serverData": "{'user': 'client1'}"
  }
}
```

<br>

---

#### webrtcConnectionCreated

Recorded when a new media stream has been established. Can be an "INBOUND" connection (the user is receiving a stream from a publisher of the session) or an "OUTBOUND" connection (the user is a publishing a stream to the session).

| Property          | Description                                                                                                                                                                       | Value                                                    |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `sessionId`       | Session for which the event was triggered                                                                                                                                         | A string with the session unique identifier              |
| `timestamp`       | Time when the event was triggered                                                                                                                                                 | UTC milliseconds                                         |
| `connectionId`   | Identifier of the participant                                                                                                                                                     | A string with the participant unique identifier          |
| `connection`      | Whether the media connection is an inbound connection (the participant is receiving media from OpenVidu) or an outbound connection (the participant is sending media to OpenVidu) | [`"INBOUND"`,`"OUTBOUND"`]                               |
| `receivingFrom`   | If `connection` is `"INBOUND"`, the participant from whom the media stream is being received                                                                                      | A string with the participant (sender) unique identifier |
| `audioEnabled`    | Whether the media connection has negotiated audio or not                                                                                                                          | [`true`,`false`]                                         |
| `videoEnabled`    | Whether the media connection has negotiated video or not                                                                                                                          | [`true`,`false`]                                         |
| `videoSource`     | If `videoEnabled` is `true`, the type of video that is being transmitted                                                                                                          | [`"CAMERA"`,`"SCREEN"`]                                  |
| `videoFramerate`  | If `videoEnabled` is `true`, the framerate of the transmitted video                                                                                                               | Number of fps                                            |
| `videoDimensions` | If `videoEnabled` is `true`, the dimensions transmitted video                                                                                                                     | String with the dimensions (e.g. `"1920x1080"`)          |

```json
{
  "webrtcConnectionCreated": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601394849759,
    "streamId": "str_CAM_GPdf_con_EIeO06zgMz",
    "connectionId": "con_ThN5Rgi8Y8",
    "connection": "INBOUND",
    "receivingFrom": "con_EIeO06zgMz",
    "videoSource": "CAMERA",
    "videoFramerate": 30,
    "videoDimensions": "{\"width\":1280,\"height\":720}",
    "audioEnabled": true,
    "videoEnabled": true
  }
}
```

<br>

---

#### webrtcConnectionDestroyed

Recorded when any media stream connection is closed.

| Property          | Description                                                                                                                                                                       | Value                                                                                                                                                                                                                                                                  |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sessionId`       | Session for which the event was triggered                                                                                                                                         | A string with the session unique identifier                                                                                                                                                                                                                            |
| `timestamp`       | Time when the event was triggered                                                                                                                                                 | UTC milliseconds                                                                                                                                                                                                                                                       |
| `connectionId`   | Identifier of the participant                                                                                                                                                     | A string with the participant unique identifier                                                                                                                                                                                                                        |
| `connection`      | Whether the media connection is an inbound connection (the participant is receiving media from OpenVidu) or an outbound connection (the participant is sending media to OpenVidu) | [`"INBOUND"`,`"OUTBOUND"`]                                                                                                                                                                                                                                             |
| `receivingFrom`   | If `connection` is `"INBOUND"`, the participant from whom the media stream is being received                                                                                      | A string with the participant (sender) unique identifier                                                                                                                                                                                                               |
| `audioEnabled`    | Whether the media connection has negotiated audio or not                                                                                                                          | [`true`,`false`]                                                                                                                                                                                                                                                       |
| `videoEnabled`    | Whether the media connection has negotiated video or not                                                                                                                          | [`true`,`false`]                                                                                                                                                                                                                                                       |
| `videoSource`     | If `videoEnabled` is `true`, the type of video that is being transmitted                                                                                                          | [`"CAMERA"`,`"SCREEN"`]                                                                                                                                                                                                                                                |
| `videoFramerate`  | If `videoEnabled` is `true`, the framerate of the transmitted video                                                                                                               | Number of fps                                                                                                                                                                                                                                                          |
| `videoDimensions` | If `videoEnabled` is `true`, the dimensions transmitted video                                                                                                                     | String with the dimensions (e.g. `"1920x1080"`)                                                                                                                                                                                                                        |
| `startTime`       | Time when the media connection was established                                                                                                                                    | UTC milliseconds                                                                                                                                                                                                                                                       |
| `duration`        | Total duration of the media connection                                                                                                                                            | Seconds                                                                                                                                                                                                                                                                |
| `reason`          | How the WebRTC connection was destroyed                                                                                                                                           | [`"unsubscribe"`,<br>`"unpublish"`,<br>`"disconnect"`,<br>`"forceUnpublishByUser"`,<br>`"forceUnpublishByServer"`,<br>`"forceDisconnectByUser"`,<br>`"forceDisconnectByServer"`,<br>`"sessionClosedByServer"`,<br>`"networkDisconnect"`,<br>`"openviduServerStopped"`,<br>`"mediaServerDisconnect"`] |

```json
{
  "webrtcConnectionDestroyed": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601394894238,
    "startTime": 1601394849759,
    "duration": 44,
    "reason": "unsubscribe",
    "streamId": "str_CAM_GPdf_con_EIeO06zgMz",
    "connectionId": "con_ThN5Rgi8Y8",
    "connection": "INBOUND",
    "receivingFrom": "con_EIeO06zgMz",
    "videoSource": "CAMERA",
    "videoFramerate": 30,
    "videoDimensions": "{\"width\":1280,\"height\":720}",
    "audioEnabled": true,
    "videoEnabled": true
  }
}
```

<br>

---

#### recordingStatusChanged

Recorded when the status of a recording has changed. The status may be:

<ul><li style="margin-bottom:4px"><code>started</code>: the session is being recorded. This means the associated video(s) already exists and its size is greater than 0. NOTE: when using COMPOSED recording with video, this event does not mean there are publisher's streams being actually recorded in the video file. It only ensures the video file exists and its size is greater than 0.</li><li style="margin-bottom:4px"><code>stopped</code>: the recording process has stopped and files are being processed. Depending on the type of OpenVidu deployment and configuration, properties <i>duration</i> and <i>size</i> can be set to 0 and <i>url</i> can be null. If this is the case, wait for status <i>ready</i> to get the final value of these properties.</li><li style="margin-bottom:4px"><code>ready</code>: the recorded file has been successfully processed and is available for download. Properties <i>duration</i>, <i>size</i> and <i>url</i> will always be properly defined at this moment. For OpenVidu Pro deployments configured to <a href="advanced-features/recording/#uploading-recordings-to-aws-s3" target="_blank">upload recordings to S3</a> this status means that the recording has been successfully stored in the S3 bucket.</li><li style="margin-bottom:4px"><code>failed</code>: the recording process has failed. The final state of the recorded file cannot be guaranteed to be stable.</li></ul>

| Property          | Description                                | Value                                         |
| ----------------- | ------------------------------------------ | --------------------------------------------- |
| `sessionId`       | Session for which the event was triggered  | A string with the session unique identifier   |
| `timestamp`       | Time when the event was triggered          | UTC milliseconds                              |
| `startTime`       | Time when the recording started            | UTC milliseconds |
| `id`              | Unique identifier of the recording         | A string with the recording unique identifier |
| `name`            | Name given to the recording file           | A string with the recording name              |
| `outputMode`      | Output mode of the recording (`COMPOSED` or `INDIVIDUAL`) | A string with the recording output mode |
| `hasAudio`        | Whether the recording file has audio or not | [`true`,`false`]                              |
| `hasVideo`        | Whether the recording file has video or not | [`true`,`false`]                              |
| `recordingLayout` | The type of layout used in the recording. Only defined if `outputMode` is `COMPOSED` and `hasVideo` is true | A **[`RecordingLayout` value](api/openvidu-java-client/io/openvidu/java/client/RecordingLayout.html){:target="_blank"}** (BEST_FIT, PICTURE_IN_PICTURE, CUSTOM ...) |
| `resolution`      | Resolution of the recorded file. Only defined if `outputMode` is `COMPOSED` and `hasVideo` is true | A string with the width and height of the video file in pixels. e.g. `"1280x720"` |
| `size`            | The size of the video file. Only guaranteed to be greater than `0` if status is `ready` | Bytes                            |
| `duration`        | Duration of the video file. Only guaranteed to be greater than `0` if status is `ready` | Seconds                          |
| `status`          | Status of the recording                    | [`"started"`,`"stopped"`,`"ready"`,`"failed"`] |
| `reason`          | Why the recording stopped. Only defined when status is _stopped_ or _ready_ | [`"recordingStoppedByServer"`,<br>`"lastParticipantLeft"`,<br>`"sessionClosedByServer"`,<br>`"automaticStop"`,<br>`"openviduServerStopped"`, <br>`"mediaServerDisconnect"`] |

```json
{
  "recordingStatusChanged": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601395005555,
    "startTime": 1601394992838,
    "duration": 8.6,
    "reason": "recordingStoppedByServer",
    "id": "ses_Jd8tUyvhXO",
    "name": "MyRecording",
    "outputMode": "COMPOSED",
    "resolution": "1920x1080",
    "recordingLayout": "BEST_FIT",
    "hasAudio": true,
    "hasVideo": true,
    "size": 1973428,
    "status": "ready"
  }
}
```

<br>

---

#### filterEventDispatched

Recorded when a filter event has been dispatched. This event can only be triggered if a filter has been applied to a stream and a listener has been added to a specific event offered by the filter. See [Voice and video filters](advanced-features/filters){:target="_blank"} to learn more.

| Property          | Description                                | Value                                         |
| ----------------- | ------------------------------------------ | --------------------------------------------- |
| `sessionId`       | Session for which the event was triggered  | A string with the session unique identifier   |
| `timestamp`       | Time when the event was triggered          | UTC milliseconds                              |
| `connectionId`   | Identifier of the participant              | A string with the participant unique identifier |
| `streamId`        | Identifier of the stream for which the filter is applied | A string with the stream unique identifier |
| `filterType`      | Type of the filter applied to the stream   | A string with the type of filter              |
| `eventType`       | Event of the filter that was triggered     | A string with the type of event               |
| `data`            | Data of the filter event                   | A string with the data returned by the filter event. Its value will depend on the type of filter and event |

```json
{
  "filterEventDispatched": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1601394994829,
    "connectionId": "con_EIeO06zgMz",
    "streamId": "str_CAM_GPdf_con_EIeO06zgMz",
    "filterType": "ZBarFilter",
    "eventType": "CodeFound",
    "data": "{timestampMillis=1568645808285, codeType=EAN-13, source=23353-1d3c_kurento.MediaPipeline/1f56f4a5-807c-71a30d40_kurento.ZBarFilter, type=CodeFound, value=0012345678905, tags=[], timestamp=1568645808}"
  }
}
```

<br>

---

#### signalSent

Recorded when a signal has been sent to a Session. Signals can be sent:

- By the clients with openvidu-browser method [Session.signal](api/openvidu-browser/classes/session.html#signal){:target="_blank"}
- By the application's server with REST API method [POST /openvidu/api/signal](reference-docs/REST-API/#post-openviduapisignal){:target="_blank"}

All kind of signals trigger `signalSent` event.

| Property          | Description                                | Value                                         |
| ----------------- | ------------------------------------------ | --------------------------------------------- |
| `sessionId` | Session for which the event was triggered  | A string with the session unique identifier         |
| `timestamp` | Time when the event was triggered          | UTC milliseconds                                    |
| `from`      | Identifier of the participant that sent the signal, or `null` if sent by the application's server | A string with the participant unique identifier, or `null` |
| `to`        | Array of participant identifiers to whom the message was addressed | An array of strings |
| `type`      | Type of the signal | A string |
| `data`      | Actual data of the signal | A string |

```json
{
  "signalSent": {
    "sessionId": "ses_Jd8tUyvhXO",
    "timestamp": 1605181948719,
    "from": "con_ZbNTYgi0ae",
    "to": ["con_Yz3To5z53q"],
    "type": "my-chat",
    "data": "{'message':'Hello!','name':'Alice'}"
  }
}
```

<br>

---

#### mediaNodeStatusChanged

<div style="
    display: table;
    border: 2px solid #0088aa9e;
    border-radius: 5px;
    width: 100%;
    margin-top: 30px;
    margin-bottom: 30px;
    padding: 10px 0 5px 0;
    background-color: rgba(0, 136, 170, 0.04);"><div style="display: table-cell; vertical-align: middle">
    <i class="icon ion-android-alert" style="
    font-size: 50px;
    color: #0088aa;
    display: inline-block;
    padding-left: 25%;
"></i></div>
<div style="
    vertical-align: middle;
    display: table-cell;
    padding-left: 20px;
    padding-right: 20px;
    ">
This event is part of <a href="openvidu-pro/" target="_blank"><strong>OpenVidu</strong><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a> tier.
</div>
</div>

Recorded when the status of a Media Node of an OpenVidu Pro cluster has changed. Below you have the finite-state machine defining the lifecycle of a Media Node and all of the possible transitions between its statuses. Visit [Scalability](openvidu-pro/scalability/#openvidu-pro-cluster-events){:target="_blank"} section for a full description of them.

<div class="row">
    <div class="pro-gallery" style="margin-bottom: 25px">
        <a data-fancybox="gallery-pro3" href="img/docs/openvidu-pro/instance-status.png"><img class="img-responsive" style="margin: auto; max-height: 600px" src="img/docs/openvidu-pro/instance-status.png"/></a>
    </div>
</div>


| Property          | Description                                | Value                                          |
| ----------------- | ------------------------------------------ | ---------------------------------------------- |
| `id`              | Unique identifier of the Media Node        | A string with the Media Node unique identifier |
| `environmentId`   | Unique identifier of the Media Node, dependent on the deployment environment. For example, an AWS EC2 machine id if the cluster is deployed in AWS | A string with the Media Node environment unique identifier |
| `ip`              | IP of the Media Node        | A string with the Media Node IP |
| `uri`             | URI of the Media Node. This is the actual direction where OpenVidu Server Pro Media Node connects to this Media Node | A string with the Media Node URI |
| `clusterId`       | OpenVidu Pro cluster identifier. This allows you to identify the specific cluster to which the Media Node triggering this event belongs, especially if you have more than one OpenVidu Pro cluster running (see ) | A string with the cluster identifier |
| `oldStatus`       | Old status of the Media Node. See [Media Node statuses](openvidu-pro/scalability/#media-node-statuses){:target="_blank"} | A string with the Media Node old status. `null` if _newStatus_ is `launching` |
| `newStatus`       | New status of the Media Node. See [Media Node statuses](openvidu-pro/scalability/#media-node-statuses){:target="_blank"} | A string with the Media Node new status |
| `timestamp`       | Time when the event was triggered          | UTC milliseconds                              |

Example:
```json
{
  "mediaNodeStatusChanged": {
    "timestamp": 1583750581667,
    "id": "media_i-1234567890abcdef0",
    "environmentId": "i-1234567890abcdef0",
    "ip": "172.17.0.3",
    "uri": "ws://172.17.0.3:8888/kurento",
    "newStatus": "running",
    "oldStatus": "launching",
    "clusterId": "MY_CLUSTER"
  }
}
```

<br>

---

#### autoscaling

<div style="
    display: table;
    border: 2px solid #0088aa9e;
    border-radius: 5px;
    width: 100%;
    margin-top: 30px;
    margin-bottom: 30px;
    padding: 10px 0 5px 0;
    background-color: rgba(0, 136, 170, 0.04);"><div style="display: table-cell; vertical-align: middle">
    <i class="icon ion-android-alert" style="
    font-size: 50px;
    color: #0088aa;
    display: inline-block;
    padding-left: 25%;
"></i></div>
<div style="
    vertical-align: middle;
    display: table-cell;
    padding-left: 20px;
    padding-right: 20px;
    ">
This event is part of <a href="openvidu-pro/" target="_blank"><strong>OpenVidu</strong><span id="openvidu-pro-tag" style="display: inline-block; background-color: rgb(0, 136, 170); color: white; font-weight: bold; padding: 0px 5px; margin-left: 5px; border-radius: 3px; font-size: 13px; line-height:21px; font-family: Montserrat, sans-serif;">PRO</span></a> tier.
</div>
</div>

Recorded when [autoscaling](openvidu-pro/scalability/#autoscaling){:target="_blank"} is enabled and the autoscaling algorithm has generated any kind of change in the status of the Media Nodes. This includes Media Nodes that must be launched and Media Nodes that must be terminated, taking into account the different statuses the Media Nodes may have in order to make the most optimal decision. That is: which specific Media Nodes must transit from which previous status to which new status in order to reach the new desired number of Media Nodes in the least possible amount of time.

For example, when a new Media Node is needed, the algorithm will always prioritize transitioning Media Nodes in `waiting-idle-to-terminate` status to `running` status, instead of launching a brand new Media Node. And if some Media Node must be removed because the load is low enough, the algorithm will always cancel any `launching` Media Node (setting its status to `canceled`) instead of removing a running one. Check out [Media Node statuses](openvidu-pro/scalability/#media-node-statuses){:target="_blank"} and [austocaling](openvidu-pro/scalability/#autoscaling){:target="_blank"} sections to learn more.

An autoscaling event will always be followed by one or more [mediaNodeStatusChanged](#medianodestatuschanged) events applying the required changes to the cluster.

| Property     | Description                                | Value                                          |
| ------------ | ------------------------------------------ | ---------------------------------------------- |
| `clusterId`  | Unique identifier of this OpenVidu Pro cluster (configuration property `OPENVIDU_PRO_CLUSTER_ID`) | A string with the OpenVidu Pro cluster unique identifier |
| `reason`     | A detailed description of why the autoscaling algorithm triggered this adjustment on the cluster size | A string with the reason of the autoscaling event |
| `mediaNodes` | An object with the Media Nodes affected by the autoscaling event | See **[mediaNodes](#medianodes)** |
| `system`     | An object with a complete description of the system regarding the state of autoscaling | See **[system](#system)** |
| `timestamp`  | Time when the event was triggered          | UTC milliseconds                              |

<br>

##### mediaNodes

| Property          | Description                                | Value                                          |
| ----------------- | ------------------------------------------ | ---------------------------------------------- |
| `launch`          | Media Nodes that are going to be added to the cluster | An object with 4 properties: <ul><li style="color: inherit"><code>total</code> : a number counting the total amount of Media Nodes that are going to be added to the cluster (sum of the following properties).</li><li style="color: inherit"><code>newNodes</code> : a number counting the amount of completely new Media Nodes that will be launched. For [On Premises](openvidu-pro/deployment/on-premises/){:target="_blank"} OpenVidu Pro clusters, this is the number of Media Nodes that must be manually launched and/or added to the cluster.</li><li style="color: inherit"><code>waitingIdleToTerminateNodes</code> : an array of Media Nodes (see <a href="#medianode"><strong>mediaNode</strong></a>) that are transitioning from <code>waiting-idle-to-terminate</code> status to <code>running</code> status.</li><li style="color: inherit"><code>canceledNodes</code> : an array of Media Nodes (see <a href="#medianode"><strong>mediaNode</strong></a>) that are transitioning from <code>canceled</code> status to <code>launching</code> status.</li></ul> |
| `terminate`       | Media Nodes that are going to be removed from the cluster | An object with 3 properties: <ul><li style="color: inherit"><code>total</code> : a number counting the total amount of Media Nodes that are going to be removed from the cluster (sum of the following properties).</li><li style="color: inherit"><code>runningNodes</code> : an array of Media Nodes (see <a href="#medianode"><strong>mediaNode</strong></a>) that are transitioning from <code>running</code> status to A) <code>waiting-idle-to-terminate</code> status, if there are ongoing sessions inside the Media Node, or B) <code>terminating</code> status, if the Media Node is empty and can be immediately removed.</li><li style="color: inherit"><code>launchingNodes</code> : an array of Media Nodes (see <a href="#medianode"><strong>mediaNode</strong></a>) that are transitioning from <code>launching</code> status to <code>canceled</code> status.</li></ul> |

##### mediaNode

| Property          | Description                                | Value                                          |
| ----------------- | ------------------------------------------ | ---------------------------------------------- |
| `id`              | Unique identifier of the Media Node        | A string with the Media Node unique identifier |
| `environmentId`   | Unique identifier of the Media Node, dependent on the deployment environment. For example, an AWS EC2 machine id if the cluster is deployed in AWS | A string with the Media Node environment unique identifier |
| `ip`              | IP of the Media Node                       | A string with the Media Node IP |
| `load`            | The CPU load of the Media Node | A decimal number between 0.00 and 100.00 |
| `status`          | Status of the Media Node. See [Media Node statuses](openvidu-pro/scalability/#media-node-statuses){:target="_blank"} | A string with the Media Node new status |

##### system

| Property | Description                                | Value                                          |
| -------- | ------------------------------------------ | ---------------------------------------------- |
| `config` | Autoscaling configuration                  | An object with 4 properties with the current autoscaling-related [configuration properties](reference-docs/openvidu-config/){:target="_blank"}: <ul><li style="color: inherit"><code>maxNodes</code> : value of configuration property <code>OPENVIDU_PRO_CLUSTER_AUTOSCALING_MAX_NODES</code> </li><li style="color: inherit"><code>minNodes</code> : value of configuration property <code>OPENVIDU_PRO_CLUSTER_AUTOSCALING_MIN_NODES</code> </li><li style="color: inherit"><code>maxAvgLoad</code> : value of configuration property <code>OPENVIDU_PRO_CLUSTER_AUTOSCALING_MAX_LOAD</code> </li><li style="color: inherit"><code>minAvgLoad</code> : value of configuration property <code>OPENVIDU_PRO_CLUSTER_AUTOSCALING_MIN_LOAD</code> </li></ul> |
| `status` | Current cluster status, including a complete description of its Media Nodes and the current load of the cluster | See **[status](#status)** |

##### status

| Property          | Description                                | Value                                          |
| ----------------- | ------------------------------------------ | ---------------------------------------------- |
| `numNodes`        | Total number of active Media Nodes in the cluster. Active nodes are those in `running` or `launching` status | A number |
| `totalLoad`       | Total CPU load of the cluster. It is calculated with the sum of all Media Nodes that may have load greater than 0: those in `running` or `waiting-idle-to-terminate` status | A decimal number |
| `avgLoad`         | The average load per Media Node. It is calculated by dividing `totalLoad` by `numNodes`. This parameter is the one compared to the limits set with [configuration properties](reference-docs/openvidu-config/){:target="_blank"} `OPENVIDU_PRO_CLUSTER_AUTOSCALING_MAX_LOAD` and `OPENVIDU_PRO_CLUSTER_AUTOSCALING_MIN_LOAD` to determine if the the cluster size must be modified | A decimal number between 0.00 and 100.00 |
| `runningNodes`    | Media Nodes in `running` status | Array of **[mediaNode](#medianode)** |
| `launchingNodes`  | Media Nodes in `launching` status | Array of **[mediaNode](#medianode)** |
| `waitingIdleToTerminateNodes` | Media Nodes in `waiting-idle-to-terminate` status | Array of **[mediaNode](#medianode)** |
| `canceledNodes`   | Media Nodes in `canceled` status | Array of **[mediaNode](#medianode)** |

Example:
```json
{
  "autoscaling": {
    "clusterId": "MY_CLUSTER",
    "reason": "The cluster average load (7.95%) is below its limits [30.00%, 70.00%] and the lower limit of Media Nodes (1) has not been reached. Current number of active nodes is 3 (2 launching and 1 running). 2 launching Media Nodes will be canceled.",
    "mediaNodes": {
      "launch": {
        "total": 0,
        "newNodes": 0,
        "waitingIdleToTerminateNodes": [],
        "canceledNodes": []
      },
      "terminate": {
        "total": 2,
        "runningNodes": [],
        "launchingNodes": [
          {
            "id": "media_i-1234567890abcdef0",
            "environmentId": null,
            "ip": null,
            "load": 0,
            "status": "launching"
          },
          {
            "id": "media_i-jfwojap393k2p332p",
            "environmentId": null,
            "ip": null,
            "load": 0,
            "status": "launching"
          }
        ]
      }
    },
    "system": {
      "config": {
        "maxNodes": 3,
        "minNodes": 1,
        "maxAvgLoad": 70,
        "minAvgLoad": 30
      },
      "status": {
        "numNodes": 3,
        "totalLoad": 23.84,
        "avgLoad": 7.946666666666666,
        "runningNodes": [
          {
            "id": "media_i-1234567890abcdef0",
            "environmentId": "i-1234567890abcdef0",
            "ip": "172.17.0.2",
            "load": 23.84,
            "status": "running"
          }
        ],
        "launchingNodes": [
          {
            "id": "media_i-jfwojap393k2p332p",
            "environmentId": "i-jfwojap393k2p332p",
            "ip": null,
            "load": 0,
            "status": "launching"
          },
          {
            "id": "media_i-po39jr3e10rkjsdfj",
            "environmentId": "i-po39jr3e10rkjsdfj",
            "ip": null,
            "load": 0,
            "status": "launching"
          }
        ],
        "waitingIdleToTerminateNodes": [],
        "canceledNodes": []
      }
    },
    "timestamp": 1592994854492
  }
}
```

<br>
