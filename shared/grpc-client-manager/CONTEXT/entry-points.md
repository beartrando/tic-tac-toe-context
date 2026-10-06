# Entry points

`import { GrpcClientManager } from "@shared/grpc-client-manager"`; типичное использование —
`new GrpcClientManager(() => new SomeClient(url, creds))` + `manager.call(...)`.
