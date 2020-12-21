---
title: Create Account Kit Simple Form
description: 15
---

<p><strong>1. Create Auth Buttons to guide user thorugh Login processes.</strong></p>

<pre>
  <div id="copy-button10" class="copy-btn" title="Copy" onclick="copyCode(this.id)">
  </div>
  <code>import 'package:flutter/material.dart';

  	Widget authButton(String text, Function function) {
  	return Container(
  	width: double.infinity,
  	padding: EdgeInsets.symmetric(vertical: 1, horizontal: 15),
  	child: RaisedButton(
  	color: Colors.blue,
  	textColor: Colors.white,
  	highlightColor: Colors.amber,
  	splashColor: Colors.amber,
  	elevation: 0,
  	shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(7)),
  	padding: EdgeInsets.symmetric(vertical: 5, horizontal: 25),
  	child: Text(text.toUpperCase()),
  	onPressed: function,
  	),
  	);
  }
  <span class="pln">
  </span>
  </code>
</pre>

<p><strong>2. Create Rich Buttons with images to guide users to which build they might choose.</strong></p>

<pre>
  <div id="copy-button11" class="copy-btn" title="Copy" onclick="copyCode(this.id)">
  </div>
  <code>
  import 'package:flutter/cupertino.dart';
  import 'package:flutter/material.dart';

  	Widget authRichButton(String textBold, String textStandart, Function function) {
  	return Container(
  	width: double.infinity,
  	padding: EdgeInsets.symmetric(vertical: 1, horizontal: 15),
  	child: RaisedButton(
  	color: Colors.blue,
  	textColor: Colors.white,
  	highlightColor: Colors.amber,
  	splashColor: Colors.amber,
  	elevation: 0,
  	shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(7)),
  	padding: EdgeInsets.symmetric(vertical: 5, horizontal: 25),
  	child: RichText(
  	text: TextSpan(
  	children: <TextSpan>[
  		TextSpan(text: textBold, style: TextStyle(fontWeight: FontWeight.bold)),
  		TextSpan(text: textStandart),
  		]
  		)),
  		onPressed: function,
  		),
  		);
  	}
 </TextSpan>
</code>
</pre>

<p><strong>3. Create the Sign in as async fuction</strong></p>
<pre><div id="copy-button11" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>_signIn() async {
  // BUILD DESIRED PARAMS
  AuthParamHelper authParamHelper = new AuthParamHelper();
  authParamHelper
  ..setIdToken()
  ..setAuthorizationCode()
  ..setAccessToken()
  ..setProfile()
  ..setEmail()
  ..setId()
  ..addToScopeList([Scope.openId])..setRequestCode(8888);
  // GET ACCOUNT INFO FROM PLUGIN
  try {
  final AuthHuaweiId accountInfo = await HmsAccount.signIn(authParamHelper);
  setState(() {
  logs.add(accountInfo.displayName);
});
} on Exception catch(exception) {
print(exception.toString());
}
/// TO VERIFY ID TOKEN, AuthParamHelper()..setIdToken()
//performServerVerification(accountInfo.idToken);
}
<span class="pln">
</span>
</code>
</pre>

<p><strong>4. Create the Sign out as async function</strong></p>
<pre><div id="copy-button12" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>   _signOut() async {
	final signOutResult = await HmsAccount.signOut();
	if (signOutResult) {
	setState(() {
	logs.add("Sign out success");
});
} else {
setState(() {
logs.add("Sign out failed");
});
}
}
<span class="pln">
</span>
</code>
</pre>

<p><strong>5. Set Silent Sign in as async function</strong></p>
<pre><div id="copy-button13" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code> _silentSignIn() async {
	AuthParamHelper authParamHelper = new AuthParamHelper();
	try {
	final AuthHuaweiId accountInfo = await HmsAccount.silentSignIn(authParamHelper);
	setState(() {
	logs.add(accountInfo.displayName);
});
} on Exception catch(exception) {
print(exception.toString());
}
}
 <span class="pln">
</span></code></pre>

<p><strong>6. Set Revoke Authorization as async function.</strong></p>
<pre><div id="copy-button14" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  _revokeAuthorization() async {
	final bool revokeResult = await HmsAccount.revokeAuthorization();
	if (revokeResult) {
	setState(() {
	logs.add("Revoked Auth Successfuly");
});
} else {
setState(() {
logs.add("Failed to Revoked Auth");
});
}
}
<span class="pln">
</span></code></pre>
<p><strong>7. Set Authorization sign in codes async funtion</strong></p>
<pre><div id="copy-button15" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  _signInWithAuthorizationCode() async {
	AuthParamHelper authParamHelper = new AuthParamHelper();
	authParamHelper..setAuthorizationCode()..setRequestCode(1002);
	try {
	final AuthHuaweiId accountInfo = await HmsAccount.signInWithAuthorizationCode(authParamHelper);
	setState(() {
	logs.add(accountInfo.authorizationCode);
});
} on Exception catch(exception) {
print(exception.toString());
}
}
<span class="pln">
</span></code></pre>

<p><strong>8. Set SMS Verifaction as async function.</strong></p>
<pre><div id="copy-button17" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>   _smsVerification() async{
	HmsAccount.smsVerification(({errorCode, message}){
	if (message != null) {
	setState(() {
	logs.add(message);
});
print("Received message: $message");
} else {
print("Error: $errorCode");
}
});
}
<span class="pln">
</span></code></pre>

<p><strong>9. For this screen we recommed you to use StatelWidgets.</strong></p>
<pre><div id="copy-button18" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div><code>  class AuthScreen extends StatefulWidget {
	@override
	_AuthScreenState createState() => _AuthScreenState();
}
 <span class="pln">
</span></code></pre>

<p><strong>10. We will conduct our interactions inside our Auth Screen State.</strong></p>

<pre><div id="copy-button18" class="copy-btn" title="Copy" onclick="copyCode(this.id)"></div>
<code>
	class _AuthScreenState extends State<AuthScreen> {
		final GlobalKey<ScaffoldState> _scaffoldKey = new GlobalKey<ScaffoldState>();

			List<String> logs = [];

				@override
				Widget build(BuildContext context) {
				return Scaffold(
				key: _scaffoldKey,
				appBar: AppBar(
				title: Text('Sign In With Huawei ID'),
				),
				body: Container(
				width: MediaQuery.of(context).size.width,
				height: MediaQuery.of(context).size.height,
				decoration: BoxDecoration(
				gradient: LinearGradient(
				colors: [Colors.white10, Colors.black87],
				begin: Alignment.topCenter,
				end: Alignment.bottomCenter
				)
				),
				child: Column(
				mainAxisAlignment: MainAxisAlignment.spaceAround,
				children: <Widget>[
					Column(
					crossAxisAlignment: CrossAxisAlignment.stretch,
					textBaseline: TextBaseline.ideographic,
					children: [
					HuaweiIdAuthButton(onPressed: _signIn, buttonColor: AuthButtonBackground.RED),
					authButton("SIGN IN WITH AUTHORIZATION CODE", _signInWithAuthorizationCode),
					authButton("SILENT SIGN IN", _silentSignIn),
					authButton("SIGN OUT", _signOut),
					authButton("REVOKE AUTHORIZATION", _revokeAuthorization),
					authButton("SMS VERIFICATION", _smsVerification),
					],
					),
					Expanded(
					child: GestureDetector(
					onDoubleTap: () {
					setState(() {
					logs.clear();
				});
			},
			child: Container(
			child: ListView.builder(
			itemCount: logs.length,
			itemBuilder: (context, index) {
			return Padding(
			padding: const EdgeInsets.all(15),
			child: Text(logs[index], style: TextStyle(color: Colors.white)),
			);
		},
		),
		),
		)
		),
		],
		),
		));
	}
</Widget>
</String>
</ScaffoldState>
</ScaffoldState>
</AuthScreen>
</code>
</pre>

<aside class="special">
  <p><strong>Note: If everything be done accuretely, the simple login process with Huawei ID will be shown as done below:</strong></p>
</aside>
<br><img style="width: 400.00px" src="https://github.com/Onurcan-Keskin/onurcan-keskin.github.io/blob/master/assets/s2.jpg?raw=true" onclick="imageclick(src)">