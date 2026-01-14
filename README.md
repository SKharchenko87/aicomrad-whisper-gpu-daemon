

```bash
mkdir -p models
cd models
wget https://raw.githubusercontent.com/ggml-org/whisper.cpp/master/models/download-ggml-model.sh
chmod +x download-ggml-model.sh
./download-ggml-model.sh large-v3-turbo
cd ..
```


```bash
mkdir -p build
cd build/
cmake .. -DGGML_CUDA=ON
cmake --build . -- -j$(nproc)
cd ..
```

```bash
./build/whisper_service  --model models/ggml-large-v3-turbo.bin --port 9090
```
или 

```bash
sudo cp ./whisper_service.service /etc/systemd/system/whisper_service.service
sudo systemctl start whisper_service.service
systemctl enable whisper_service.service
```


Запуск
```bash
docker run --rm -it --gpus all -p 9090:8080  -v ./models/ggml-large-v3.bin:/models/ggml-large-v3.bin   aicomrad-whisper:latest ./whisper-server --model /models/ggml-large-v3.bin --host 0.0.0.0 --port 8080
```


```bash
# Формат на выходе json
curl -v -X POST http://localhost:9090/inference   -H "Content-Type: multipart/form-data"   -F "file=@/home/skh/a_16k_mono.wav"   -F "model=large-v3" -F "response_format=json" -F "language=ru"
# Формат на выходе подробный json
curl -v -X POST http://localhost:9090/inference   -H "Content-Type: multipart/form-data"   -F "file=@/home/skh/a_16k_mono.wav"   -F "model=large-v3" -F "response_format=verbose_json" -F "language=ru"
# Формат субтитр
curl -v -X POST http://localhost:9090/inference   -H "Content-Type: multipart/form-data"   -F "file=@/home/skh/a_16k_mono.wav"   -F "model=large-v3" -F "response_format=srt" -F "language=ru"
# Формат субтитр
curl -v -X POST http://localhost:9090/inference   -H "Content-Type: multipart/form-data"   -F "file=@/home/skh/a_16k_mono.wav"   -F "model=large-v3" -F "response_format=vtt" -F "language=ru"
# Просто текст
curl -v -X POST http://localhost:9090/inference   -H "Content-Type: multipart/form-data"   -F "file=@/home/skh/a_16k_mono.wav"   -F "model=large-v3" -F "response_format=text" -F "language=ru"

```