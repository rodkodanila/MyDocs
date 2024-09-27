# containerd
# Убедитесь, что в /etc/containerd/config.toml задано:
plugins."io.containerd.grpc.v1.cri".registry.config_path = "/etc/containerd/certs.d"
# Создайте файл /etc/containerd/certs.d/docker.io/hosts.toml с содержимым:
server = "https://registry-1.docker.io"
host."https://your-registry-mirror-url".capabilities = ["pull", "resolve"]
# Где https://your-registry-mirror-url замените на URL нужного зеркала.

# Перезапустите containerd.

[статья](https://cloud.ru/docs/cce/ug/topics/faq__common__how-to-change-repo.html#faq-common-how-to-change-repo-change-public-repo-containerd)