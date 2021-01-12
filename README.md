# Google SafetyNet Attestation for React Native

## About SafetyNet

Google provides an API to verify device integrity and detect harmful apps. See the [SafetyNet documentation](https://developer.android.com/training/safetynet/index.html) for more information.

## Getting Started

`$ npm install @bitwala/react-native-safetynet`

or

`$ yarn add @bitwala/react-native-safetynet`

### Mostly Automatic Installation

`$ react-native link @bitwala/react-native-safetynet`

### Manual installation

#### Android

1. Open up `android/app/src/main/java/[...]/MainActivity.java`

- Add `import com.bitwala.safetynet.RNSafetyNetPackage;` to the imports at the top of the file
- Add `new RNSafetyNetPackage()` to the list returned by the `getPackages()` method

2. Append the following lines to `android/settings.gradle`:
   ```
   include ':@bitwala/react-native-safetynet'
   project(':@bitwala/react-native-safetynet').projectDir = new File(rootProject.projectDir, 	'../node_modules/@bitwala/react-native-safetynet/android')
   ```
3. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
   ```
     implementation project(':@bitwala/react-native-safetynet')
   ```

## Usage

```javascript
import RNSafetyNetClient from '@bitwala/react-native-safetynet';

// STEP 1: Send nonce request to server with device unique id
const deviceId =
  'You can use the "react-native-device-info" module to get the unique id of device';

const nonceResult = await RNSafetyNetClient.requestNonce({
  endPointUrl: 'https://your-server/nonce-creator-endpoint',
  additionalData: deviceId,
});

if (!nonceResult.nonce || nonceResult.error) {
  console.log(nonceResult.error);
  return;
}

// STEP 2: Send attestation request to Google Play

const attestationResult = await RNSafetyNetClient.sendAttestationRequest(
  nonceResult.nonce,
  'YOUR API KEY'
);

if (!attestationResult.jws || attestationResult.error) {
  console.log('Attestation request failed.');
  return;
}

// STEP 3: Pass the JSW to your server for verification.

const verification = await RNSafetyNetClient.verifyAttestationResult({
  endPointUrl: 'https://your-server/verification-endpoint',
  attestationJws: attestationResult.jws,
});
```
