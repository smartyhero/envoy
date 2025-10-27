# Bazel + TypeScript 生成（示例）

目的：使用 Bazel 在仓库内以与其他语言相同的方式生成 TypeScript 类型/服务代码（基于 ts-proto）。

目录：本例把配置放在 api/ts。生成结果会输出到 bazel-bin/api/ts/gen/（也可以改为 api/javascript/ 等以与现有语言一致）。

快速指南：
1. 安装依赖（在 api/ts 目录）：
   - npm install
   或
   - yarn install

2. WORKSPACE 配置（示例见 WORKSPACE.snippet）：推荐使用 rules_nodejs 的 npm_install/yarn_install，
   使 Bazel 可以在 genrule 中可访问到 node_modules 中的 protoc 插件二进制（protoc-gen-ts_proto）。

3. 本地测试（不使用 Bazel）：
   在 api/ts 目录运行：
   npm run gen:proto

4. 使用 Bazel 生成：
   从仓库根运行：
   bazel build //api/ts:ts_proto_gen

   生成产物位于：bazel-bin/api/ts/gen/

注意：
- 本示例使用 ts-proto 作为 protoc 插件。你也可以改用其他插件（grpc-web、protoc-gen-ts 等），但需要调整 genrule。
- 若要严格保持与其他语言的目录一致，建议把输出目录映射为 api/javascript 或 api/typescript，并在仓库中为该目录创建对应的 Bazel targets/filegroups。
- 在 CI 中建议把生成步骤作为 Bazel target 或独立 job，并在 PR 中要求生成产物是 up-to-date。

调试提示：
- 如果出现 plugin 未找到，请在 WORKSPACE 中正确配置 npm_install/yarn_install，并把插件路径改为 external workspace（如 @npm//bin/protoc-gen-ts_proto ）或将插件作为 Bazel 工具声明并传入 genrule 的 tools 列表.
