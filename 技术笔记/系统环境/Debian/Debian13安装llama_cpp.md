sudo apt update
sudo apt install -y vulkan-tools libvulkan-dev spirv-tools glslang-tools glslc spirv-headers
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
mkdir build && cd build
cmake .. -DGGML_VULKAN=ON
pip install huggingface-hub
# 设置正确的环境变量
export HF_ENDPOINT=https://hf-mirror.com

# 重新运行你的下载命令
hf download Jackrong/Qwen3.5-9B-DeepSeek-V4-Flash-GGUF --local-dir ./qwen9b Qwen3.5-9B-DeepSeek-V4-Flash-Q8_0.gguf
