# Cài đặt VNC trên máy ảo Docker

Thực hiện viết `Dockerfile` để cài đặt và thiết lập VNC và DE.

```Dockerfile
# Ảnh gốc: Arch Linux cơ sở.
FROM archlinux:base
# Chạy một loạt lệnh để thiết lập.
RUN <<ENDRUN
# Khởi tạo Pacman.
pacman-key --init
# Cập nhật các gói, cài đặt display server là Xorg, DE là KDE Plasma và phần mềm VNC là TigerVNC.
pacman -Syu --noconfirm --needed xorg-server plasma-desktop tigervnc
mkdir -p ~/.vnc
# Gán người dùng root cho cổng đầu tiên của VNC.
echo ':1=root' > /etc/tigervnc/vncserver.users
# Cấu hình VNC.
cat << ENDCAT > ~/.vnc/config
session=plasma
geometry=1600x900
ENDCAT
# Thiết lập mật khẩu VNC.
echo "Testing." | vncpasswd -f > ~/.vnc/passwd
chmod 0600 ~/.vnc/passwd
ENDRUN
# Mở cổng VNC để cho phép kết nối từ bên ngoài.
EXPOSE 5901
# Lệnh chạy máy chủ VNC khi container được khởi động.
CMD dbus-launch vncserver :1
```

Tạo một tệp cấu hình Docker Compose (`compose.yaml`) để chạy máy ảo VNC:

```json
{
	"services": {
		"desktop": {
			"build": ".",
			"ports": ["5901:5901"]
		}
	}
}
```

Dựng và chạy máy ảo bằng lệnh `docker compose up`.

Video demo thao tác với máy ảo qua VNC:

![Demo VNC trên Docker](https://raw.githubusercontent.com/leduyquang753/software-architecture-exercises/main/Tu%E1%BA%A7n%201/Demo%20VNC%20tr%C3%AAn%20Docker.mp4)