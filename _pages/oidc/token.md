---
title: OpenID Connect
lead: >
    [OpenID Connect](http://openid.net){:class="usa-link--external"} is a simple identity layer built on top of the OAuth 2.0 protocol. Login.gov supports [version 1.0](http://openid.net/specs/openid-connect-core-1_0.html){:class="usa-link--external"} of the specification and conforms to the [iGov Profile](https://openid.net/wg/igov){:class="usa-link--external"}.
sidenav:
  - text: Getting started
    href: "oidc/getting-started/"
  - text: Authorization
    href: "oidc/authorization/"
  - text: Token
    href: "oidc/token/"
    links:
      - text: Request parameters
        href: "#request-parameters"
      - text: Token response
        href: "#token-response"
  - text: User info
    href: "oidc/user-info/"
  - text: Certificates
    href: "oidc/certificates/"
  - text: Logout
    href: "oidc/logout/"

---
{% capture client_assertion %}
A [JWT](https://jwt.io/){:class="usa-link--external"} signed with the client’s private key (minimum length of 2048 bits) using the RS256 algorithm and containing the following claims:
- **iss** (string) — The issuer, which must be the `client_id`.
- **sub** (string) — The subject, which must also be the `client_id`.
- **aud** (string) — The audience, which should be (or, in the case of multiple audience values, include) the URL of the token endpoint, for example: `https://idp.int.identitysandbox.gov/api/openid_connect/token`
- **jti** (string) — The JWT ID, a unique identifier for the token which can be used to prevent reuse of the token. Should be an unguessable, random string generated by the client.
- **exp** (number) — The expiration time for this token. Should be an integer timestamp (number of seconds since the [Unix Epoch](https://en.wikipedia.org/wiki/Unix_time){:class="usa-link--external"}) and be a short period of time in the future (such as 5 minutes from now).
{% endcapture %}


<div class="grid-row grid-gap">
  <div class="desktop:grid-col-8 mobile:grid-col-full">
    <h2>Token</h2>
    <p>Clients use the token endpoint to exchange the authorization code for an  <code class="language-plaintext highlighter-rouge">access_token</code>. This token is needed to access the user info endpoint. To request a token, send a HTTP POST request to the /api/openid_connect/token endpoint.</p> 
    <p>View an example for <strong>private_key_jwt</strong> or <strong>PKCE</strong> in the side panel.</p>
    <h3 class="margin-top-4" id="request-parameters">Request Parameters</h3>
    <ul class="doc-sub-nav">
      <li id="jwt-nav" class="doc-sub-nav-item code-button__selected margin-left-neg-3">JWT</li>
      <li id="pkce-nav" class="doc-sub-nav-item margin-left-3">PKCE</li>
    </ul>
    <div class="dev-doc-row jwt-only">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">client_assertion</h4>
          <span class="float-left text-italic">required for private_key_jwt</span>
        </div>
        <div class="grid-col-7">
            {{ client_assertion | markdownify }}
        </div>
      </div>
    </div>
    <div class="dev-doc-row jwt-only">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">client_assertion_type</h4>
          <span class="float-left text-italic">required for private_key_jwt</span>
        </div>
        <div class="grid-col-7">
          When using private_key_jwt, must be <code class="language-plaintext highlighter-rouge">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</code>
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters clearfix">code</h4>
        </div>
        <div class="grid-col-7 padding-bottom-2">
            The authorization code returned by the <a class="usa-link" href="{{ site.baseurl }}/oidc/authorization/#code">authorization response</a>.
        </div>
      </div>
    </div>
    <div class="dev-doc-row pkce-only" hidden>
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters clearfix">code_verifier</h4><span class="float-left text-italic">required for PKCE</span>
        </div>
        <div class="grid-col-7">
            The original value (before the SHA256) generated for the authorization request for PKCE that corresponds to the <code class="language-plaintext highlighter-rouge">code_challenge</code>.
        </div>
      </div>
    </div>
    <div class="grid-row dev-doc-row">
      <div class="grid-col-5">
        <h4 class="parameters clearfix">grant_type</h4>
      </div>
      <div class="grid-col-7">
          <code class="language-plaintext highlighter-rouge">authorization_code</code>
      </div>
    </div>
  </div>
  <div class="usa-layout-docs__main code-snippet-column desktop:grid-col-4">
      <section id="pkce" class="code-snippet-section" hidden>
        <span class="code-button code-button__selected margin-left-2">PKCE Request</span>
          {% include snippets/oidc/token/pkce.md %}
      </section>
      <section id="jwt" class="code-snippet-section">
        <span class="code-button code-button__selected margin-left-2">JWT Request</span>
          {% include snippets/oidc/token/jwt.md %}
      </section>
  </div>
</div>
<div class="grid-row grid-gap">
  <div class="desktop:grid-col-8 mobile:grid-col-full">
    <h3 id="token-response" class="margin-top-6">Token response</h3>
    <p>The token response will be a JSON object containing the following:</p>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">access_token<span class="text-normal"> (string)</span></h4>
        </div>
        <div class="grid-col-7">
          An opaque token used to access the <a href="{{ site.baseurl }}/oidc/#user-info" class="usa-link">user info endpoint</a>.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">token_type <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">
          The type of access token, which will always be <code class="language-plaintext highlighter-rouge">Bearer</code>.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">expires_in <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">
          The number of seconds the access token will expire.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">id_token <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">
          A signed <a href="https://jwt.io/" class="usa-link usa-link--external">JWT</a> that contains basic attributes about the user and it is signed using the <code class="language-plaintext highlighter-rouge">RS256</code> algorithm. The public key used to verify this JWT is available from the <a href="{{ site.baseurl }}/oidc/#certificates" class="usa-link">certificates</a> endpoint.
        </div>
      </div>
    </div>
    <p>The <code class="language-plaintext highlighter-rouge">id_token</code> contains the following claims:</p>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">iss <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">
         The issuer of the response, which will be the URL of the Login.gov IdP, for example: <code class="language-plaintext highlighter-rouge">https://idp.int.identitysandbox.gov</code>.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">sub <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">
          The subject identifier, the UUID of the Login.gov user (see <a href="{{ site.baseurl }}/attributes/" class="usa-link">user attributes</a>).
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">aud <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">
          The audience, which will be the <code class="language-plaintext highlighter-rouge">client_id</code>
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">acr <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">       
          The Authentication Context Class Reference value of the returned claims, from the original <a href="{{ site.baseurl }}/oidc/authorization/" class="usa-link">authorization request</a>.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">at_hash <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">       
          The access token hash, a URL-safe base-64 encoding of the left 128 bits of the SHA256 of the <code class="language-plaintext highlighter-rouge">access_token</code> value. Provided so the client can verify the <code class="language-plaintext highlighter-rouge">access_token</code> value.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">c_hash <span class="text-normal">(string)</span></h4>
        </div>
        <div class="grid-col-7">       
          The code hash, a URL-safe base-64 encoding of the left 128 bits of the SHA256 of the authorization <code class="language-plaintext highlighter-rouge">code</code> value. Provided so the client can verify the <code class="language-plaintext highlighter-rouge">code</code> value.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">exp <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">       
          The expiration time for this token, an integer timestamp representing the number of seconds since the Unix Epoch.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">iat <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">       
          Time at which the JWT was issued, an integer timestamp representing the number of seconds since the Unix Epoch.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">jti <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">       
          The JWT ID, a unique identifier for the token which can be used to prevent reuse of the token. Should be an unguessable, random string generated by the client.
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">nbf <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">       
          The “not before” value, an integer timestamp of when the token will start to be valid (number of seconds since the Unix Epoch).
        </div>
      </div>
    </div>
    <div class="dev-doc-row">
      <div class="grid-row">
        <div class="grid-col-5">
          <h4 class="parameters">nonce <span class="text-normal">(number)</span></h4>
        </div>
        <div class="grid-col-7">       
          The nonce value provided by the client in the <a class="usa-link" href="{{ site.baseurl }}/oidc/authorization/">authorization request</a>. A unique value, at least 22 characters in length, used to verify the integrity of the <code class="language-plaintext highlighter-rouge">id_token</code> and mitigate <a href="https://en.wikipedia.org/wiki/Replay_attack" class="usa-link usa-link--external">replay attacks</a>. This value should include per-session state and be unguessable by attackers. Read more about <a href="http://openid.net/specs/openid-connect-core-1_0.html#NonceNotes" class="usa-link usa-link--external">nonce implementation</a> in the spec.
        </div>
      </div>
    </div>
    <a href="{{ site.baseurl }}/oidc/user-info/" class="usa-link margin-top-4 mobile:display-none desktop:display-block">Next step: User info</a>
  </div>
  <div class="usa-layout-docs__main code-snippet-column desktop:grid-col-4">
    <section class="code-snippet-section margin-top-2 position-relative z-index-1">
      <button id="oidc_token_tab1_button" data-selector="oidc_token" class="code-button code-button__selected margin-left-2">Response</button>
      <button id="oidc_token_tab2_button" data-selector="oidc_token" class="code-button margin-left-2">Decoded id_token</button>
      <section id="oidc_token_tab1">
        {% include snippets/oidc/token/response.md %}
      </section>
      <section id="oidc_token_tab2" hidden>
        {% include snippets/oidc/token/token.md %}
      </section>
    </section>
  </div>
  <a href="{{ site.baseurl }}/oidc/user-info/" class="usa-link mobile:display-block desktop:display-none margin-top-2">Next step: User info</a>
</div>