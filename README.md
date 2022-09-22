# flutter_local_storage_and_video

A new Flutter project.

## Hasil Praktikum

<img src="images/hasil.gif">

## Persiapan

- Menambahkan plugin image_picker dan video_player di pubspec.yaml

      dependencies:
        cupertino_icons: ^1.0.2
        flutter:
          sdk: flutter
        image_picker:
        video_player:

- Menambahkan permission untuk menerapkan plugin video_player

  - Android

    [project root]/android/app/src/main/AndroidManifest.xml.

        <manifest xmlns:android="http://schemas.android.com/apk/res/android">
          <application ...>

          </application>

          <uses-permission android:name="android.permission.INTERNET"/>
        </manifest>

  - IOS

    [project root]/ios/Runner/Info.plist

        <key>NSAppTransportSecurity</key>
        <dict>
          <key>NSAllowsArbitraryLoads</key>
          <true/>
        </dict>

## Praktikum Upload File

- Membuat class Statefull widget
- Menambahkan variabel di class State
  Menambahkan variabel 'image' yang mengembalikan nilai bertipe XFile, dan variabel 'picker' yang mengembalikan instance dari ImagePicker().

      XFile? image;

      final ImagePicker picker = ImagePicker();

- Membuat fungsi yang akan nilai berisi gambar berdasarkan sumber gambar yang dipilih sebelumnya (galeri/kamera). Kemudian nilai gambar disimpan ke dalam variabel image yang bertipe XFile yang sudah dibuat sebelumnya.

       Future getImage(ImageSource media) async {
         var img = await picker.pickImage(source: media);

         setState(() {
            image = img;
          });
       }

- Membuat fungsi yang akan menampilkan widget AlertDialog. Di dalam widget tersebut akan menampilkan 2 button yang menjadi opsi media yang bisa dipilih oleh pengguna. Setiap button akan memanggil fungsi getImage yang sebelumnya, kemudian untuk nilai parameter mengembalikan yang bertipe ImageSource.

      getImage(ImageSource.gallery);

      getImage(ImageSource.camera);

- Pada method build akan mengembalikan widget Scaffold, di dalam widget Scaffold membuat sekumpulan widget di dalam Column. Pada widget Column akan menampilkan widget button yang mentrigger fungsi AlertDialog dan hasil gambar yang dipilih. Jika belum dipilih maka menampilkan teks 'No Image'. Jika dipilih, maka gambar akan dipanggil menggunkana class File yang diisi oleh nilai 'path' dari variabel image.

       child: Image.file(
        ...
        File(image!.path),
        ...
      ),

## Praktikum Video Player

- Membuat class Stateful widget
- Menambahkan variabel di class State
  Menambahkan variabel '\_controller' yang mengembalikan VideoPlayerController, dan variabel '\_initializeVideoPlayerFuture' yang akan menyimpan Future dari VideoPlayerController.initialize

      late VideoPlayerController _controller;
      late Future<void> _initializeVideoPlayerFuture;

- Menginisialisasi controller ke dalam method initState. Controller akan memanggil konstruktor yang akan menampilkan video menggunakan jaringan. Di dalamnya menerima parameter wajib berupa sumber video.

      _controller = VideoPlayerController.network(
            'https://flutter.github.io/assets-for-api-docs/assets/videos/butterfly.mp4',
          );

- Kemudian \_controller akan method initialize() yang disimpan ke dalam method '\_initializeVideoPlayerFuture'.

      _initializeVideoPlayerFuture = _controller.initialize();

- Untuk memutar video, dipanggil dengan widget VideoPlayer yang sudah disediakan pluginnya. Agar video ditampilkan dengan ukuran yang sesuai, maka widget VideoPlayer perlu dimasukkan widget AspectRatio.

      return AspectRatio(
        aspectRatio: _controller.value.aspectRatio,
        // Use the VideoPlayer widget to display the video.
        child: VideoPlayer(_controller),
      );

- Pemutaran video perlu menunggu controller selesai diinisialisasi, sehingga widget yang dibuat sebelumnya perlu dimasukkan ke dalam FutureBuilder.
- Jika selesai diinisialisasi maka aplikasi akan memutar video ketika pengguna klik button play, jika belum maka akan menampilkan loading.

      FutureBuilder(
        future: _initializeVideoPlayerFuture,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.done) {
            return AspectRatio(
              aspectRatio: _controller.value.aspectRatio,
              child: VideoPlayer(_controller),
            );
          } else {
            return const Center(
              child: CircularProgressIndicator(),
            );
          }
        },
      )
