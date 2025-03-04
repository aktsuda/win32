---
description: Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client.
ms.assetid: eaa15fed-4438-4e43-9be3-aa100ca453c7
title: AcceptSecurityContext (General) function (Sspi.h)
ms.topic: reference
ms.date: 07/25/2019
---

# AcceptSecurityContext (General) function

The **AcceptSecurityContext (General)** function enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client. The remote client uses the [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md) function to start the process of establishing a [*security context*](../secgloss/s-gly.md). The server can require one or more reply tokens from the remote client to complete establishing the [*security context*](../secgloss/s-gly.md).

For information about using this function with a specific [*security support provider*](../secgloss/s-gly.md) (SSP), see the following topics.



| Topic                                                                          | Description                                                                                                                                                                                                                                                                      |
|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [**AcceptSecurityContext (CredSSP)**](acceptsecuritycontext--credssp.md)     | Allows the server component of a transport application establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client by using Credential Security Support Provider (CredSSP).<br/> |
| [**AcceptSecurityContext (Digest)**](acceptsecuritycontext--digest.md)       | Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client that is using Digest.<br/>                                                                                                                  |
| [**AcceptSecurityContext (Kerberos)**](acceptsecuritycontext--kerberos.md)   | Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client that is using Kerberos.<br/>                                                                                                                |
| [**AcceptSecurityContext (Negotiate)**](acceptsecuritycontext--negotiate.md) | Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client that is using Negotiate.<br/>                                                                                                               |
| [**AcceptSecurityContext (NTLM)**](acceptsecuritycontext--ntlm.md)           | Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client that is using NTLM.<br/>                                                                                                                    |
| [**AcceptSecurityContext (Schannel)**](acceptsecuritycontext--schannel.md)   | Enables the server component of a transport application to establish a [*security context*](../secgloss/s-gly.md) between the server and a remote client that is using Schannel.<br/>                                                                                                                |



 

## Syntax


```C++
SECURITY_STATUS SEC_Entry AcceptSecurityContext(
  _In_opt_    PCredHandle    phCredential,
  _Inout_opt_ PCtxtHandle    phContext,
  _In_opt_    PSecBufferDesc pInput,
  _In_        ULONG          fContextReq,
  _In_        ULONG          TargetDataRep,
  _Inout_opt_ PCtxtHandle    phNewContext,
  _Inout_opt_ PSecBufferDesc pOutput,
  _Out_       PULONG         pfContextAttr,
  _Out_opt_   PTimeStamp     ptsExpiry
);
```



## Parameters

<dl> <dt>

*phCredential* \[in, optional\]
</dt> <dd>

A handle to the credentials of the server. The server calls the [**AcquireCredentialsHandle (General)**](acquirecredentialshandle--general.md) function with either the SECPKG\_CRED\_INBOUND or SECPKG\_CRED\_BOTH flag set to retrieve this handle.

</dd> <dt>

*phContext* \[in, out\]
</dt> <dd>

A pointer to a [CtxtHandle](sspi-handles.md) structure. On the first call to **AcceptSecurityContext (General)**, this pointer is **NULL**. On subsequent calls, *phContext* is the handle to the partially formed context that was returned in the *phNewContext* parameter by the first call.

</dd> <dt>

*pInput* \[in, optional\]
</dt> <dd>

A pointer to a [**SecBufferDesc**](/windows/win32/api/sspi/ns-sspi-secbufferdesc) structure generated by a client call to [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md) that contains the input buffer descriptor.

When using the Schannel SSP, the first buffer must be of type SECBUFFER\_TOKEN and contain the security token received from the client. The second buffer should be of type SECBUFFER\_EMPTY.

When using the Negotiate, Kerberos, or NTLM SSPs, channel binding information can be specified by passing in a [**SecBuffer**](/windows/win32/api/sspi/ns-sspi-secbuffer) structure of type **SECBUFFER\_CHANNEL\_BINDINGS** in addition to the buffers generated by the call to the [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md) function. The channel binding information for the channel binding buffer can be obtained by calling the [**QueryContextAttributes (Schannel)**](querycontextattributes--schannel.md) function on the Schannel context the client used to authenticate.

</dd> <dt>

*fContextReq* \[in\]
</dt> <dd>

Bit flags that specify the attributes required by the server to establish the context. Bit flags can be combined by using bitwise-**OR** operations. This parameter can be one or more of the following values.



| Value                                                                                                                                                                                                                                          | Meaning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span id="ASC_REQ_ALLOCATE_MEMORY"></span><span id="asc_req_allocate_memory"></span><dl> <dt>**ASC\_REQ\_ALLOCATE\_MEMORY**</dt> </dl>                                                  | Digest and Schannel will allocate output buffers for you. When you have finished using the output buffers, free them by calling the [**FreeContextBuffer**](/windows/win32/api/sspi/nf-sspi-freecontextbuffer) function.<br/>                                                                                                                                                                                                                                                                                |
| <span id="ASC_REQ_ALLOW_MISSING_BINDINGS"></span><span id="asc_req_allow_missing_bindings"></span><dl> <dt>**ASC\_REQ\_ALLOW\_MISSING\_BINDINGS**</dt> </dl>                            | Indicates that Digest does not require channel bindings for both inner and outer channels. This value is used for backward compatibility when support for endpoint channel binding is not known.<br/> This value is mutually exclusive with **ASC\_REQ\_PROXY\_BINDINGS**.<br/> This value is supported only by the Digest SSP.<br/> **Windows Server 2008, Windows Vista, Windows Server 2003 and Windows XP:** This value is not supported.<br/> <br/> |
| <span id="ASC_REQ_CONFIDENTIALITY"></span><span id="asc_req_confidentiality"></span><dl> <dt>**ASC\_REQ\_CONFIDENTIALITY**</dt> </dl>                                                   | Encrypt and decrypt messages. <br/> The Digest SSP supports this flag for SASL only.<br/>                                                                                                                                                                                                                                                                                                                                                                                  |
| <span id="ASC_REQ_CONNECTION"></span><span id="asc_req_connection"></span><dl> <dt>**ASC\_REQ\_CONNECTION**</dt> </dl>                                                                  | The [*security context*](../secgloss/s-gly.md) will not handle formatting messages.<br/>                                                                                                                                                                                                                                                                                                                                                                                                                   |
| <span id="ASC_REQ_DELEGATE"></span><span id="asc_req_delegate"></span><dl> <dt>**ASC\_REQ\_DELEGATE**</dt> </dl>                                                                        | The server is allowed to impersonate the client. Valid for Kerberos. Ignore this flag for [*constrained delegation*](../secgloss/c-gly.md).<br/>                                                                                                                                                                                                                                                             |
| <span id="ASC_REQ_EXTENDED_ERROR"></span><span id="asc_req_extended_error"></span><dl> <dt>**ASC\_REQ\_EXTENDED\_ERROR**</dt> </dl>                                                     | When errors occur, the remote party will be notified.<br/>                                                                                                                                                                                                                                                                                                                                                                                                                       |
| <span id="ASC_REQ_HTTP__0x10000000_"></span><span id="asc_req_http__0x10000000_"></span><span id="ASC_REQ_HTTP__0X10000000_"></span><dl> <dt>**ASC\_REQ\_HTTP (0x10000000)**</dt> </dl> | Use Digest for HTTP. Omit this flag to use Digest as an SASL mechanism.<br/>                                                                                                                                                                                                                                                                                                                                                                                                     |
| <span id="ASC_REQ_INTEGRITY"></span><span id="asc_req_integrity"></span><dl> <dt>**ASC\_REQ\_INTEGRITY**</dt> </dl>                                                                     | Sign messages and verify signatures.<br/> Schannel does not support this flag.<br/>                                                                                                                                                                                                                                                                                                                                                                                        |
| <span id="ASC_REQ_MUTUAL_AUTH"></span><span id="asc_req_mutual_auth"></span><dl> <dt>**ASC\_REQ\_MUTUAL\_AUTH**</dt> </dl>                                                              | The client is required to supply a certificate to be used for client authentication.<br/> This flag is supported only by Schannel.<br/>                                                                                                                                                                                                                                                                                                                                    |
| <span id="ASC_REQ_PROXY_BINDINGS"></span><span id="asc_req_proxy_bindings"></span><dl> <dt>**ASC\_REQ\_PROXY\_BINDINGS**</dt> </dl>                                                     | Indicates that Digest requires channel binding.<br/> This value is mutually exclusive with **ASC\_REQ\_ALLOW\_MISSING\_BINDINGS**.<br/> This value is supported only by the Digest SSP.<br/> **Windows Server 2008, Windows Vista, Windows Server 2003 and Windows XP:** This value is not supported.<br/> <br/>                                                                                                                                         |
| <span id="ASC_REQ_REPLAY_DETECT"></span><span id="asc_req_replay_detect"></span><dl> <dt>**ASC\_REQ\_REPLAY\_DETECT**</dt> </dl>                                                        | Detect replayed packets.<br/>                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span id="ASC_REQ_SEQUENCE_DETECT"></span><span id="asc_req_sequence_detect"></span><dl> <dt>**ASC\_REQ\_SEQUENCE\_DETECT**</dt> </dl>                                                  | Detect messages received out of sequence.<br/>                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| <span id="ASC_REQ_STREAM"></span><span id="asc_req_stream"></span><dl> <dt>**ASC\_REQ\_STREAM**</dt> </dl>                                                                              | Support a stream-oriented connection.<br/> This flag is supported only by Schannel.<br/>                                                                                                                                                                                                                                                                                                                                                                                   |



 

For possible attribute flags and their meanings, see [Context Requirements](context-requirements.md). Flags used for this parameter are prefixed with ASC\_REQ, for example, ASC\_REQ\_DELEGATE.

The requested attributes may not be supported by the client. For more information, see the *pfContextAttr* parameter.

</dd> <dt>

*TargetDataRep* \[in\]
</dt> <dd>

The data representation, such as byte ordering, on the target. This parameter can be either SECURITY\_NATIVE\_DREP or SECURITY\_NETWORK\_DREP.

This parameter is not used with Schannel or Digest SSPs. When you use Schannel or Digest SSPs, specify zero for this parameter.

</dd> <dt>

*phNewContext* \[in, out, optional\]
</dt> <dd>

A pointer to a [CtxtHandle](sspi-handles.md) structure. On the first call to **AcceptSecurityContext (General)**, this pointer receives the new context handle. On subsequent calls, *phNewContext* can be the same as the handle specified in the *phContext* parameter.

</dd> <dt>

*pOutput* \[in, out, optional\]
</dt> <dd>

A pointer to a [**SecBufferDesc**](/windows/win32/api/sspi/ns-sspi-secbufferdesc) structure that contains the output buffer descriptor. This buffer is sent to the client for input into additional calls to [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md). An output buffer may be generated even if the function returns SEC\_E\_OK. Any buffer generated must be sent back to the client application.

When using Schannel, on output, this buffer receives a token for the [*security context*](../secgloss/s-gly.md). The token must be sent to the client. The function can also return a buffer of type SECBUFFER\_EXTRA. In addition, the caller must pass in a buffer of type **SECBUFFER\_ALERT**. On output, if an alert is generated, this buffer contains information about that alert, and the function fails.

</dd> <dt>

*pfContextAttr* \[out\]
</dt> <dd>

A pointer to a variable that receives a set of bit flags that indicate the attributes of the established context. For a description of the various attributes, see [Context Requirements](context-requirements.md). Flags used for this parameter are prefixed with ASC\_RET, for example, ASC\_RET\_DELEGATE.

Do not check for security-related attributes until the final function call returns successfully. Attribute flags not related to security, such as the ASC\_RET\_ALLOCATED\_MEMORY flag, can be checked before the final return.

</dd> <dt>

*ptsTimeStamp* \[out, optional\]
</dt> <dd>

A pointer to a [**TimeStamp**](timestamp.md) structure that receives the expiration time of the context. We recommend that the [*security package*](../secgloss/s-gly.md) always return this value in local time.

This parameter is set to a constant maximum time. There is no expiration time for Digest [*security context*](../secgloss/s-gly.md)s or credentials or when using the Digest SSP.

This is optional when using the Schannel SSP. When the remote party has supplied a certificate to be used for authentication, this parameter receives the expiration time for that certificate. If no certificate was supplied, a maximum time value is returned.

> [!Note]  
> Until the last call of the authentication process, the expiration time for the context can be incorrect because more information will be provided during later stages of the negotiation. Therefore, *ptsTimeStamp* must be **NULL** until the last call to the function.

 

</dd> </dl>

## Return value

This function returns one of the following values.



<table><colgroup><col  /><col  /></colgroup><thead><tr class="header"><th>Return code/value</th><th>Description</th></tr></thead><tbody><tr class="odd"><td><dl> <dt><strong>SEC_E_BAD_BINDINGS</strong></dt> <dt>0x80090346L</dt> </dl></td><td>The function failed. Channel binding policy was not satisfied.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_INCOMPLETE_MESSAGE</strong></dt> <dt>0x80090318L</dt> </dl></td><td>The function succeeded. The data in the input buffer is incomplete. The application must read additional data from the client and call [<strong>AcceptSecurityContext (General)</strong>](acceptsecuritycontext--general.md) again.<br/> This value can be returned when using the Schannel SSP. For more information about this return value, see [<strong>AcceptSecurityContext (Schannel)</strong>](acceptsecuritycontext--schannel.md).<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_E_INSUFFICIENT_MEMORY</strong></dt> <dt>0x80090300L</dt> </dl></td><td>The function failed. There is not enough memory available to complete the requested action.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_INTERNAL_ERROR</strong></dt> <dt>0x80090304L</dt> </dl></td><td>The function failed. An error occurred that did not map to an SSPI error code.<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_E_INVALID_HANDLE</strong></dt> <dt>0x80100003L</dt> </dl></td><td>The function failed. The handle passed to the function is not valid.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_INVALID_TOKEN</strong></dt> <dt>0x80090308L</dt> </dl></td><td>The function failed. The token passed to the function is not valid.<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_E_LOGON_DENIED</strong></dt> <dt>0x8009030CL</dt> </dl></td><td>The logon failed.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_NO_AUTHENTICATING_AUTHORITY</strong></dt> <dt>0x80090311L</dt> </dl></td><td>The function failed. No authority could be contacted for authentication. This could be due to the following conditions:<br/><ul><li>The domain name of the authenticating party is incorrect.</li><li>The domain is unavailable.</li><li>The trust relationship has failed.</li></ul></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_E_NO_CREDENTIALS</strong></dt> <dt>0x8009030EL</dt> </dl></td><td>The function failed. The credentials handle specified in the <em>phCredential</em> parameter is not valid. This value can be returned when using the Digest or Schannel SSP.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_OK</strong></dt> <dt>0x00000000L</dt> </dl></td><td>The function succeeded. The [*security context*](../secgloss/s-gly.md) received from the client was accepted. If an output token was generated by the function, it must be sent to the client process.<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_E_SECURITY_QOS_FAILED</strong></dt> <dt>0x80090332L</dt> </dl></td><td>The function failed. A context attribute flag that is not valid was specified in the <em>fContextReq</em> parameter. This value can be returned when using the Digest SSP.<br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_E_UNSUPPORTED_FUNCTION</strong></dt> <dt>0x80090302L</dt> </dl></td><td>The function failed. A context attribute flag that is not valid (ASC_REQ_DELEGATE or ASC_REQ_PROMPT_FOR_CREDS) was specified in the <em>fContextReq</em> parameter. This value can be returned when using the Schannel SSP.<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_I_COMPLETE_AND_CONTINUE</strong></dt> <dt>0x00090314L</dt> </dl></td><td>The function succeeded. The server must call [<strong>CompleteAuthToken</strong>](/windows/win32/api/sspi/nf-sspi-completeauthtoken) and pass the output token to the client. The server then waits for a return token from the client and then makes another call to [<strong>AcceptSecurityContext (General)</strong>](acceptsecuritycontext--general.md). <br/></td></tr><tr class="even"><td><dl> <dt><strong>SEC_I_COMPLETE_NEEDED</strong></dt> <dt>0x00090313L</dt> </dl></td><td>The function succeeded. The server must finish building the message from the client and then call the [<strong>CompleteAuthToken</strong>](/windows/win32/api/sspi/nf-sspi-completeauthtoken) function.<br/></td></tr><tr class="odd"><td><dl> <dt><strong>SEC_I_CONTINUE_NEEDED</strong></dt> <dt>0x00090312L</dt> </dl></td><td>The function succeeded. The server must send the output token to the client and wait for a returned token. The returned token should be passed in <em>pInput</em> for another call to [<strong>AcceptSecurityContext (General)</strong>](acceptsecuritycontext--general.md).<br/></td></tr><tr class="even"><td><dl> <dt><strong>STATUS_LOGON_FAILURE</strong></dt> <dt>0xC000006DL</dt> </dl></td><td>The function failed. The [<strong>AcceptSecurityContext (General)</strong>](acceptsecuritycontext--general.md) function was called after the specified context was established. This value can be returned when using the Digest SSP.<br/></td></tr></tbody></table>



 

## Remarks

The **AcceptSecurityContext (General)** function is the server counterpart to the [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md) function.

When the server receives a request from a client, the server uses the *fContextReq* parameter to specify what it requires of the session. In this fashion, a server can specify that clients must be capable of using a confidential or [*integrity*](../secgloss/i-gly.md)-checked session, and it can reject clients that cannot meet that demand. Alternatively, a server can require nothing, and whatever the client can provide or requires is returned in the *pfContextAttr* parameter.

For a package that supports multiple-leg authentication, such as mutual authentication, the calling sequence is as follows:

1.  The client transmits a token to the server.
2.  The server calls **AcceptSecurityContext (General)** the first time, which generates a reply token that is then sent to the client.
3.  The client receives the token and passes it to [**InitializeSecurityContext (General)**](initializesecuritycontext--general.md). If **InitializeSecurityContext (General)** returns SEC\_E\_OK, mutual authentication has been completed and a secure session can begin. If **InitializeSecurityContext (General)** returns an error code, the mutual authentication negotiation ends. Otherwise, the security token returned by **InitializeSecurityContext (General)** is sent to the client, and steps 2 and 3 are repeated.

The *fContextReq* and *pfContextAttr* parameters are bitmasks that represent various context attributes. For a description of the various attributes, see [Context Requirements](context-requirements.md).

> [!Note]  
> The *pfContextAttr* parameter is valid on any successful return, but only on the final successful return should you examine the flags pertaining to security aspects of the context. Intermediate returns can set, for example, the ISC\_RET\_ALLOCATED\_MEMORY flag.

 

The caller is responsible for determining whether the final context attributes are sufficient. If, for example, confidentiality (encryption) was requested, but could not be established, some applications may choose to shut down the connection immediately. If the [*security context*](../secgloss/s-gly.md) cannot be established, the server must free the partially created context by calling the [**DeleteSecurityContext**](/windows/win32/api/sspi/nf-sspi-deletesecuritycontext) function. For information about when to call the **DeleteSecurityContext** function, see **DeleteSecurityContext**.

After the [*security context*](../secgloss/s-gly.md) has been established, the server application can use the [**QuerySecurityContextToken**](/windows/win32/api/sspi/nf-sspi-querysecuritycontexttoken) function to retrieve a handle to the user account to which the client certificate was mapped. Also, the server can use the [**ImpersonateSecurityContext**](/windows/win32/api/sspi/nf-sspi-impersonatesecuritycontext) function to impersonate the user.

## Requirements



| Requirement | Value |
|-------------------------------------|--------------------------------------------------------------------------------------------------------|
| Minimum supported client<br/> | Windows XP \[desktop apps only\]<br/>                                                            |
| Minimum supported server<br/> | Windows Server 2003 \[desktop apps only\]<br/>                                                   |
| Header<br/>                   | <dl> <dt>Sspi.h (include Security.h)</dt> </dl> |
| Library<br/>                  | <dl> <dt>Secur32.lib</dt> </dl>                 |
| DLL<br/>                      | <dl> <dt>Secur32.dll</dt> </dl>                 |



## See also

<dl> <dt>

[SSPI Functions](authentication-functions.md#sspi-functions)
</dt> <dt>

[**DeleteSecurityContext**](/windows/win32/api/sspi/nf-sspi-deletesecuritycontext)
</dt> <dt>

[**InitializeSecurityContext (General)**](initializesecuritycontext--general.md)
</dt> </dl>

 

 
