## Notes

- Must be run on device
- Frames are obtained with extension of `didOutput` method from `CameraFeedManagerDelegate`
  - within the extension there is a call to `runModel` of `ModelDataHandler` to access the `Interpreter` class of TFL
- Pods -> TFLSwift
- `ModelDataHandler` class handles preprocessing, makes an inference using `Interpreter` on a certain frame, and returns the top results
- `CVPixelBuffer` returns camera data from above `didOutput` method
  - Remove alpha to leave only RGB
```swift
  // Copy the RGB data to the input `Tensor`.
  try interpreter.copy(rgbData, toInputAt: 0)
  // Run inference by invoking the `Interpreter`.
  try interpreter.invoke()
  // Get the output `Tensor` to process the inference results.
  outputTensor = try interpreter.output(at: 0)
```
- finally
```swift
  // Get the quantized results from the output tensor's `data` property.
let quantizedResults = [UInt8](outputTensor.data)
// Dequantize the results using the quantization values.
let results = quantizedResults.map {
  quantization.scale * Float(Int($0) - quantization.zeroPoint)
}
```
- then the results are sorted to get the top inference results


https://www.tensorflow.org/lite/models/image_classification/overview
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/experimental/swift