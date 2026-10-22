| Term                          | Meaning                                                                                                                   |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **API**                       | The interface your code uses to produce telemetry                                                                         |
| **SDK**                       | A library that implements the OpenTelemetry API                                                                           |
| **Instrumented library**      | The library that telemetry is being collected _from_                                                                      |
| **Instrumentation library**   | The library that _provides_ that instrumentation. It can be the same library, if the library has instrumentation built in |
| **Automatic instrumentation** | Collecting telemetry without changing the application's source code                                                       |
| **Propagators**               | Components that serialise and deserialise context, such as span context and baggage                                       |
| **Sampling**                  | Controlling how much data is exported, most often for traces                                                              |
| **Exporter**                  | Sends telemetry to a consumer, by push or pull                                                                            |
| **Collector**                 | A vendor-neutral program that receives, processes, and exports telemetry, as an agent or a gateway                        |
| **Receiver**                  | The part of the Collector that defines how telemetry is received                                                          |
| **Observability backend**     | Receives, stores, and lets you query telemetry. OpenTelemetry is _not_ a backend                                          |
| **Observability frontend**    | The user interface for visualising and analysing telemetry                                                                |
