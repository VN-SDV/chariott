# Dự án Eclipse Chariott

- [Trạng thái CI](#ci-status)
- [Chariott là gì?](#what-is-chariott)
- [Cách phát triển với Chariott](#how-to-develop-with-chariott)
   - [Thuật ngữ](#thuật ngữ)
   - [Khái niệm về ý định](#khái niệm về ý định)
- [Yêu cầu](#requirements)
- [Bắt đầu](#bắt đầu)
   - [Dev Container](#dev-container)
   - [Tạo tất cả các tệp nhị phân và chạy thử nghiệm](#build-all-binaries-and-run-tests)
   - [Sử dụng Podman thay vì Docker](#using-podman-instead-of-docker)
- [Cách chạy ví dụ và tương tác với Chariott](#how-to-run-the-examples-and-interact-with-chariott)
- [Cách chạy demo chế độ chó](#how-to-run-the-dog-mode-demo)
- [Nhãn hiệu](#trademarks)

## Tình trạng CI

<!-- VIỆC CẦN LÀM: Thêm lại sau khi chúng tôi chuyển sang Repo mới -->

- Rỉ CI
- E2E CI
- Kiểm toán an ninh

## Chariott là gì?

Chariott là dịch vụ [gRPC](https://grpc.io) cung cấp giao diện chung để tương tác với các ứng dụng.
Các ứng dụng giao tiếp với nhau thông qua Chariott. Chariott cung cấp các dịch vụ cần thiết để kích hoạt
quản lý vòng đời ứng dụng và giao tiếp giữa các ứng dụng. Điều này được thực hiện bằng cách đăng ký ứng dụng
một _intent_ mà Chariott sau đó sẽ _hoàn thành_ bằng cách trung gian giao tiếp với ứng dụng thích hợp để
thực hiện ý định đó. Các ứng dụng đáp ứng những ý định này được gọi là _providers_. Bạn có thể tìm thêm thông tin về thiết kế Chariott bằng sơ đồ [tại đây](./docs/design/README.md).

## Cách phát triển với Chariott

Chariott cung cấp giao diện gRPC để tương tác từ ứng dụng khách. Các
ứng dụng khách có thể được viết bằng bất kỳ ngôn ngữ nào hỗ trợ gRPC. Các
các ví dụ trong kho lưu trữ này được viết bằng Rust, nhưng các khái niệm tương tự áp dụng cho
bất kỳ ngôn ngữ nào.

### Thuật ngữ

| Kỳ hạn | Mô tả |
| --- | --- |
| Ứng dụng | Là ứng dụng, chúng tôi mô tả một khách hàng đang tương tác với Chariott để tra cứu các nhà cung cấp và tương tác với họ thông qua Chariott bằng cách sử dụng ý định |
| Nhà cung cấp | Nhà cung cấp cũng là một ứng dụng và ngoài ra, còn đăng ký các không gian tên với các ý định mà nó hỗ trợ cho các điểm cuối Chariott mà các ứng dụng khác có thể sử dụng thông qua Chariott |

### Khái niệm về ý định

Ý định là cách chính để tương tác với Chariott. Khi một nhà cung cấp đăng ký
một ý định với Chariott, các ứng dụng khác có thể sử dụng ý định đó để tương tác với
nhà cung cấp. Mục đích là một phương thức gRPC được xác định trong tệp của nhà cung cấp
định nghĩa protobuf Định nghĩa đó chỉ được sử dụng bởi chính Chariott.

Chariott cũng cung cấp giao diện gRPC để các ứng dụng tương tác với
các nhà cung cấp và ủy quyền các cuộc gọi dựa trên ý định cho nhà cung cấp một cách minh bạch.
Do đó, khách hàng không cần biết vị trí và thông tin chi tiết của nhà cung cấp miễn là
ý định của họ được thực hiện.

Dưới đây là danh sách các ý định được hỗ trợ hiện tại:

| ý định | Mô tả |
| --- | --- |
| Khám phá | Truy xuất giao diện gốc của nhà cung cấp. Điều này rất hữu ích nếu bạn cần tương tác cụ thể với nhà cung cấp mà bạn biết là có sẵn trong hệ thống và bạn không muốn sử dụng Chariott để tương tác với nhà cung cấp đó. Điều này cũng được sử dụng để truy xuất các điểm cuối phát trực tuyến của nhà cung cấp. |
| Kiểm tra | Hỗ trợ kiểm tra chức năng, thuộc tính và sự kiện bằng cú pháp truy vấn đơn giản. |
| Gọi | Gọi một phương thức trên một nhà cung cấp. |
| Theo dõi | Theo dõi các sự kiện của một nhà cung cấp. Lưu ý rằng điều này không mở kênh phát trực tuyến, điều này được thực hiện thông qua điểm cuối phát trực tuyến gốc của nhà cung cấp. |
| Đọc | Đọc một tài sản của một nhà cung cấp. |
| Viết | Viết một tài sản cho một nhà cung cấp. |

Bạn có thể tìm thêm thông tin trong các định nghĩa protobuf trong `./proto`.

Có một tài liệu riêng mô tả các ứng dụng ví dụ và
kịch bản được hỗ trợ bởi Chariott. Nó có thể được tìm thấy
[tại đây](./examples/applications/README.md).

## Yêu cầu

Nguồn hiện tại được phát triển và thử nghiệm trong WSL2/Linux chạy Ubuntu 20.04
trên kiến trúc AMD64. Nó không được thử nghiệm với bất kỳ cấu hình nào khác. Bạn
có thể gặp phải tình trạng thiếu hỗ trợ cho các nền tảng khác, nhưng vui lòng
góp phần thu hẹp khoảng cách.

## Bắt đầu

### Vùng chứa nhà phát triển

Để phát triển và chạy các ví dụ, chúng tôi khuyên bạn nên sử dụng
Mẫu [Devcontainer](https://code.visualstudio.com/docs/remote/containers)
được cung cấp tại `.devcontainer/devcontainer.json`. Nếu bạn quyết định không sử dụng
Devcontainer, hãy tham khảo `devcontainer.json` để biết danh sách các plugin/công cụ
chúng tôi sử dụng.

> Lưu ý: Nếu bạn sử dụng Devcontainers và bạn đang chạy trên Windows, hãy đảm bảo kiểm tra
> kho lưu trữ trên hệ thống tệp WSL2 trong bản phân phối đích mà bạn đang sử dụng.

### Xây dựng tất cả các tệp nhị phân và chạy thử nghiệm

```bash
xây dựng hàng hóa --workspace
kiểm tra hàng hóa --workspace
```

### Sử dụng Podman thay vì Docker

Nếu bạn muốn sử dụng Podman, bạn phải [kích hoạt Podman trong Visual Studio
Code][vscode-podman] và cập nhật tệp `.devcontainer/devcontainer.json`
với những bổ sung sau:

   [vscode-podman]: https://code.visualstudio.com/remote/advancedcontainers/docker-options#_podman

```jsonc
{
   //...
   "runArgs": [
     "--cap-add=SYS_PTRACE",
     "--bảo mật-opt",
     "seccomp=không giới hạn",
     "--userns=keep-id"
   ],
   //...
   "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind,Z",
   "workspaceFolder": "/workspace",
   "containerUser": "vscode",
   //...
}
```

> **LƯU Ý**: Vui lòng sử dụng tên thư mục không gian làm việc khác.

## Cách chạy ví dụ và tương tác với Chariott

Vì giao thức truyền thông vượt trội của Chariott là gRPC, nên sự tương tác với
các ví dụ được thực hiện thông qua gRPC. Để minh họa cách gọi các phương thức gRPC, chúng tôi
sử dụng công cụ dòng lệnh [grpcurl](https://github.com/fullstorydev/grpcurl) với ứng dụng ví dụ
**kv-ứng dụng**. **kv-app** là kho lưu trữ khóa-giá trị có thể được sử dụng để lưu trữ
và đọc trạng thái. Trạng thái được lưu trữ trong bộ nhớ và không được duy trì. Nó cũng thể hiện
việc sử dụng các thùng `ess` và `keyvalue`.

Hướng dẫn này được mô tả trong [ví dụ kv-app README](examples/applications/kv-app/README.md).

## Cách chạy bản demo chế độ chó

Để chạy demo chế độ chó, vui lòng tham khảo [bản demo chế độ chó](./examples/applications/README.md).

## Nhãn hiệu

Dự án này có thể chứa các nhãn hiệu hoặc biểu tượng cho các dự án, sản phẩm hoặc dịch vụ. Sử dụng được ủy quyền của Microsoft
nhãn hiệu hoặc logo phải tuân theo và phải tuân theo
[Nguyên tắc nhãn hiệu và thương hiệu của Microsoft](https://www.microsoft.com/legal/intellectualproperty/trademarks/usage/general).
Việc sử dụng nhãn hiệu hoặc logo của Microsoft trong các phiên bản sửa đổi của dự án này không được gây nhầm lẫn hoặc ngụ ý tài trợ của Microsoft.
Bất kỳ việc sử dụng nhãn hiệu hoặc biểu trưng của bên thứ ba nào đều phải tuân theo các chính sách của bên thứ ba đó.
