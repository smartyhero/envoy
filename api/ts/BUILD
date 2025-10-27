# DO NOT EDIT UNLESS YOU KNOW WHAT YOU'RE DOING.
# 示例 BUILD：把生成 TypeScript 的 genrule（基于 protoc + ts-proto）放在 api/ts 下。
# 根据你的 WORKSPACE 中如何安装 node/npm 包（npm_install/yarn_install），可能需要调整插件路径或把插件暴露为 Bazel 工具。
package(default_visibility = ["//visibility:public"])

filegroup(
    name = "proto_files",
    srcs = glob(["../**/*.proto"]),
)

genrule(
    name = "ts_proto_gen",
    srcs = [":proto_files"],
    outs = ["gen/_DONE"],
    cmd = """
set -e
OUT_DIR=$(RULEDIR)/gen
rm -rf "${OUT_DIR}"
mkdir -p "${OUT_DIR}"

# protoc 二进制 - 在 CI 请确保 protoc 可用
PROTOC=protoc

# 默认尝试从 workspace 的 node_modules 找到 protoc-gen-ts_proto
# 如果你使用 bazel npm_install/yarn_install，请把插件二进制路径改为相应的 external workspace 路径（见 README）。
PLUGIN_BIN="$(pwd)/node_modules/.bin/protoc-gen-ts_proto"

if [ ! -x "$PLUGIN_BIN" ]; then
  echo "Warning: protoc-gen-ts_proto not found at $PLUGIN_BIN; please adjust plugin path in BUILD."
fi

# Bazel 会把 $(locations ...) 展开为 SRCs 的路径列表（相对路径）
for p in $(locations $(SRCS)); do
  "$PROTOC" -I=api \
    --plugin=protoc-gen-ts_proto="$PLUGIN_BIN" \
    --ts_proto_out="${OUT_DIR}" \
    --ts_proto_opt=esModuleInterop=true,forceLong=string,useOptionals=messages \
    "$p"
 done

# marker
mkdir -p $(dirname $(OUTS))
touch $(OUTS)
""",
    visibility = ["//visibility:public"],
)
