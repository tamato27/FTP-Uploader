# FTP-Uploader ising only ftplib & OS
Uploads files to a ftp server and creates logs
Also moves files from upload dir to completed directory
Moves files from FEDEXPORT to upload dir , then uploads to ftp server , thereafter moves to completed folder and checks for any duplicate files and finally writes to a log file

File structure
c:\EDI\
  .. FEDEXPORT
  .. FTP
    .. Completed
    .. Logs
    .. Upload
