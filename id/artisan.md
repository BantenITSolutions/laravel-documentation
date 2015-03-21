# Artisan CLI

- [Pengantar](#introduction)
- [Pemakaian](#usage)
- [Memanggil Perintah dari Luar CLI](#calling-commands-outside-of-cli)
- [Penjadwalan Perintah Artisan](#scheduling-artisan-commands)

<a name="introduction"></a>
## Pengantar

Artisan adalah nama dari antarmuka baris perintah (command-line) yang disertakan bersama Laravel. Ini memberikan sejumlah perintah yang membantu untuk penggunaan Anda ketika mengembangkan aplikasi Anda. Hal ini didorong oleh kuat komponen Symfony Console.

<a name="usage"></a>
## Pemakaian

#### Daftar Semua Perintah yang Tersedia

Untuk melihat daftar semua perintah Artisan yang tersedia, Anda dapat menggunakan perintah `list`:

	php artisan list

#### Melihat Layar Bantuan Untuk Sebuah Perintah

Setiap perintah juga menyertakan "bantuan" layar yang menampilkan dan menjelaskan argumen dan pilihan perintah yang tersedia. Untuk melihat layar bantuan, cukup mengawali nama perintah dengan `help`:

	php artisan help migrate

#### Menentukan Konfigurasi Lingkungan

Anda dapat menentukan lingkungan konfigurasi yang harus digunakan saat menjalankan perintah dengan menggunakan `beralih --env`:

	php artisan migrate --env=local

#### Menampilkan Versi Laravel Saat ini

Anda juga dapat melihat versi saat ini dari instalasi Laravel Anda menggunakan opsi `--version`:

	php artisan --version

<a name="calling-commands-outside-of-cli"></a>
## Memanggil Perintah dari Luar CLI

Kadang-kadang Anda mungkin ingin menjalankan perintah Artisan diluar CLI. Sebagai contoh, Anda mungkin ingin menembak perintah Artisan dari rute HTTP. Cukup gunakan `Artisan` fasad:

	Route::get('/foo', function()
	{
		$exitCode = Artisan::call('command:name', ['--option' => 'foo']);

		//
	});

Anda bahkan dapat mengantri Artisan perintah sehingga mereka diproses di latar belakang dengan [pekerja antrian] Anda (/docs/5.0/antrian):

	Route::get('/foo', function()
	{
		Artisan::queue('command:name', ['--option' => 'foo']);

		//
	});

<a name="scheduling-artisan-commands"></a>
## Penjadwalan Perintah Artisan

Di masa lalu, pengembang telah menghasilkan entri Cron untuk setiap perintah konsol mereka ingin jadwalkan. Namun, ini adalah pusing. Jadwal konsol tidak lagi dalam sumber kontrol, dan Anda harus SSH ke server Anda untuk menambahkan entri Cron. Mari kita membuat hidup kita lebih mudah. Perintah scheduler Laravel memungkinkan Anda untuk lancar dan ekspresif menentukan jadwal perintah Anda dalam Laravel sendiri, dan hanya masuk Cron tunggal diperlukan pada server Anda.

Jadwal perintah Anda disimpan dalam `app/Console/Kernel.php` berkas. Dalam kelas ini Anda akan melihat metode `schedule`. Untuk membantu Anda memulai, contoh sederhana disertakan dengan metode. Anda bebas untuk menambahkan sebanyak pekerjaan dijadwalkan Anda ingin obyek `Schedule`. Satu-satunya entri Cron Anda perlu menambahkan ke server Anda adalah ini:

	* * * * * php /path/to/artisan schedule:run 1>> /dev/null 2>&1

Cron ini akan memanggil perintah scheduler Laravel setiap menit. Kemudian, Laravel mengevaluasi pekerjaan Anda yang dijadwalkan dan menjalankan pekerjaan yang jatuh tempo. Ini tidak bisa lebih mudah!

### Contoh Penjadwalan Lainnya

Mari kita lihat beberapa contoh penjadwalan lainnya:

#### Penjadwalan Closures

	$schedule->call(function()
	{
		// Do some task...

	})->hourly();

#### Penjadwalan Perintah Terminal

	$schedule->exec('composer self-update')->daily();

#### Pernyataan Cron Manual

	$schedule->command('foo')->cron('* * * * *');

#### Frekuensi Jobs

	$schedule->command('foo')->everyFiveMinutes();

	$schedule->command('foo')->everyTenMinutes();

	$schedule->command('foo')->everyThirtyMinutes();

#### Jobs Harian

	$schedule->command('foo')->daily();

#### Jobs sehari-hari pada waktu tertentu (Waktu 24 Jam)

	$schedule->command('foo')->dailyAt('15:00');

#### Jobs Dua Kali Sehari

	$schedule->command('foo')->twiceDaily();

#### Job yang Berjalan Setiap hari kerja

	$schedule->command('foo')->weekdays();

#### Job Mingguan

	$schedule->command('foo')->weekly();

	// Schedule weekly job for specific day (0-6) and time...
	$schedule->command('foo')->weeklyOn(1, '8:00');

#### Job Bulanan

	$schedule->command('foo')->monthly();

#### Job yang Berjalan Pada Hari Tertentu

	$schedule->command('foo')->mondays();
	$schedule->command('foo')->tuesdays();
	$schedule->command('foo')->wednesdays();
	$schedule->command('foo')->thursdays();
	$schedule->command('foo')->fridays();
	$schedule->command('foo')->saturdays();
	$schedule->command('foo')->sundays();

#### Membatasi Lingkungan Jobs yang Harus Jalankan

	$schedule->command('foo')->monthly()->environments('production');

#### Menunjukkan Job yang Harus dijalankan Bahkan Ketika Aplikasi Apakah Dalam Mode Maintenance

	$schedule->command('foo')->monthly()->evenInMaintenanceMode();

#### Hanya Mengizinkan Job Untuk dijalankan Ketika Callback adalah Benar

	$schedule->command('foo')->monthly()->when(function()
	{
		return true;
	});

#### Emailkan Output dari Sebuah Job yang Telah Dijadwalkan

	$schedule->command('foo')->sendOutputTo($filePath)->emailOutputTo('foo@example.com');

> **Catatan:** Anda harus mengirim output ke file sebelum dapat dikirimkan.

#### Kirim Output Dari Job yang Dijadwalkan ke Sebuah Lokasi yang Diberikan

	$schedule->command('foo')->sendOutputTo($filePath);

#### Ping Sebuah URL yang Diberikan Setelah Job Dijalankan

	$schedule->command('foo')->thenPing($url);
