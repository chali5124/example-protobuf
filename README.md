protobuf 리포지토리 예제

### overview
복수의 runtime환경을 위한 공유되는 별도의 protobuf 리포지토리 예제
1. supports
   - nestjs(typescript)
   - golang
2. structure
   1. .github
   2. google
   3. domains
   4. generated

### development
1. pre-requirements
   1. install buf and protoc
      ```
      brew install bufbuild/buf/buf
      brew install protobuf
      ```
2. build and generate
   1. with buf
      1. files
         1. buf.yaml
            - 기본 구성을 설정합니다.
         2. buf.gen.v2.yaml (v2)
            - 기본적인 경우 v2를 사용합니다.
            - protoc-gen-ts_proto를 npm으로 로컬에 설치한 후 buf.gen.yaml에서 plugins.local로 설정해 사용할 수 있습니다.
         3. buf.gen.v1.yaml (v1)
            - outer api gateway의 경우 v1에서 안정적으로 설치 및 generate됩니다. 따라서 별도로 설정 운영합니다.
      2. to generate
         - grpc-gateway가 v2를 지원하지 않음
         ```
         # version 1 for golang, grpc-gateway
         buf generate --clean --template buf.gen.v1.yaml .
         # version 2 for nestjs
         buf generate --clean --template buf.gen.v2.yaml .
         ```
   2. with protoc
      기본적으로 protoc를 통해 generate 합니다. 관리의 원활함을 위해 buf를 통해 관리하는 것을 권장합니다. protoc를 통해 generate 하는 방법은 아래와 같습니다. 2. typescript
      inner api의 서비스는 현재 nestjs로 구현되어 있으므로 nestjs 의 서비스 및 컨트롤러 파일을 생성하기 위해서는 프로토콜 버퍼 파일을 타입스크립트 파일로 변환합니다.
      1. install protoc plugin [protoc-gen-ts_proto](https://github.com/stephenh/ts-proto)
      ```
      npm install protoc-gen-ts_proto
      ```
      2. to generate
      ```
      protoc --plugin=./node_modules/.bin/protoc-gen-ts_proto --ts_proto_out=generated --ts_proto_opt=nestJs=true ./src/chali5124/shared/v1/code.proto
      ```
      2. go
         outer의 proxy api gateway는 go로 구현되어 있으므로 프로토콜 버퍼 파일을 go 파일로 변환합니다.
         1. install protoc plugin
         ```
         go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
         go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
         ```
         2. generate
         ```
         protoc --go_out=generated --go-grpc_out=generated --go-grpc_opt=require_unimplemented_servers=false ./src/chali5124/shared/v1/code.proto
         ```
3. plugins
   1. buf
      1. https://github.com/bufbuild/buf
      2. https://buf.build/docs/introduction
   2. protovalidate
      1. https://github.com/bufbuild/protovalidate/tree/main

### use
   1. golang
      - 프로젝트에서 example-protobuf 사용기히 위해 private repogitory 로 설정
      ```
      go env -w GOPRIVATE=github.com/myroteam1
      ```
   2. nestjs
      - submodule 추가후 import 설정이 필요함
      - example-api-with-grpc 참고
