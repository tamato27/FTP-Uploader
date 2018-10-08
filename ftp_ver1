import ftplib
import os
import shutil
from _datetime import datetime
import time

# FTP Connection properties 
#server = "server_ip"
#user = "user"
#passwd = "password"

# servers directory (source dir) & (remote ftp directory)
#server_dir = "/ToVB"
#local_dir = "C:/EDI"

# Time stamps to log file
start_time = datetime.now()
started_time = datetime.now()
time = start_time.strftime("%Y-%m-%d %H:%M")
start_time_log = str(time) + " Started the file upload process" + "\n"

# create current date filename
date = datetime.now()
date = date.strftime("%Y-%m-%d")
#os.chdir(local_dir + "/FEDEXPORT")


def make_copy(source):
    """
    Makes a backup copy of all the files in dir
    :return:
    """
    log_file(start_time_log, source)  # Write start timer to log file

    # change to correct directory tree
    os.chdir(source + "/FEDEXPORT")
    source_cwd = os.getcwd()
    source_dir_list = os.listdir(source_cwd)

    # Change to Upload directory
    os.chdir(source + "/FTP/Upload")
    upload_dir = os.getcwd()
    upload_dir_list = os.listdir(upload_dir)

    # count the number of files in the source directory
    total_files = get_file_count(source_cwd)
    tot = date + " " + "FILE: Total files in source directory is: " + str(total_files)
    log_file(tot, source)

    # Move files from export to upload dir
    for file in source_dir_list:
        if file in upload_dir_list:
            os.remove(source_cwd + "/" + file)
            log = date + " " + file + " FILE: already exists in upload dir removing duplicates"
            log_file(log, source)  # log to file
        else:
            shutil.move(source_cwd + "/" + file, upload_dir)
            #shutil.copy(source_cwd + "/" + file, upload_dir)
            log = file + " Moved"
            log_file(log, source)

    # count the number of files in the upload directory
    total_files = get_file_count(upload_dir)
    tot = date + " " + "Total files in upload directory is: " + str(total_files) + "\n"
    log_file(tot, source)


def upload_files(source, destination):
    """
    upload files from destination to server
    :param source:
    :param destination:
    :return returns the upload directory
    """

    # connect to ftp
    ftp = ftplib.FTP(server)
    ftp.login(user, passwd)

    # Change passv
    #ftp.set_pasv(False)

    # change to the current directory of ftp
    ftp.cwd(destination)
    print("destination = " + str(ftp.cwd(destination)))
    # get list of files in ftp destination directory
    ftp_dir_list = ftp.nlst()
    ftp_dir = ftp.nlst(destination)

    # get and set source directory
    os.chdir(source + "/FTP/Upload")
    source_dir = os.getcwd()
    source_dir_list = os.listdir(source_dir)

    for file in source_dir_list:
        if file in ftp_dir_list:
            # ftp.storlines("STOR file", file)
            msg = date + " " + file + " FTP: Exists on FTP : " + " Not Uploaded"
            log_file(msg, source)
        else:
            # read = open(file, 'rb')
            # ftp.storbinary("STOR %s" % file, read)
            # ftp.storbinary('STOR file', open(file, 'rb'))
            ftp.storbinary('STOR %s' % file, open(os.path.join(source_dir, file), "rb"))
            msg = date + " " + file + " FTP: Uploaded to FTP"
            log_file(msg, source)  # log event
            

    # close ftp session
    ftp.close()


def move_files(source):
    """
    Moves uploaded files to the completed folder and skips duplicates then cleans the upload directory
    """

    os.chdir(source + "/FTP/Upload")
    source_dir = os.getcwd()
    source_dir_list = os.listdir(source_dir)

    os.chdir(source + "/FTP/Completed")
    destination = os.getcwd()
    destination_list = os.listdir(destination)

    for file in source_dir_list:
        if file in destination_list:
            os.remove(source_dir + "/" + file)
            msg0 = date + " " + file + " exists in folder did not move"
            log_file(msg0, source)  # log to file
        else:
            shutil.move(source_dir + "/" + file, destination)
            msg2 = date + " " + "Moved to Completed folder"
            log_file(msg2, source)  # log to file


def download_files(source, destination):
    """
    to download all files located in a given ftp server
    :param source:
    :param destination:
    :return:
    """
    
    # connect to ftp
    ftp = ftplib.FTP(server)
    ftp.login(user, passwd)

    # set the current directory on the server
    ftp.cwd(source)

    # get the list of files in ftp dir
    filelist = ftp.nlst()

    for file in filelist:
        #print("Downloading " + file)
        ftp.retrbinary("RETR " + file, open(destination + file, 'wb').write)

    # close ftp session
    ftp.close()


def log_file(message, source=local_dir):
    """
    creating a log file with filename as current date
    """

    # change to log dir
    os.chdir(source + "/FTP/Logs")
    destination = os.getcwd()
    destination_list = os.listdir(destination)  # all files in dir

    with open(date + ".txt", "a") as text_file:
        text_file.write(message + "\n")


def get_file_count(source):
    """
    funtion to count all files in directories :return: the value of the counted files in the directories
    """
    
    dir_list = os.listdir(source)
    total_files = len(dir_list)
    return total_files


# 1 call function to make copy of files
make_copy(local_dir)

# 2 call function to upload to ftp and skipping duplicates
upload_files(local_dir, server_dir)

# 3 call function move
move_files(local_dir)

# 5 call function to download from ftp
# dl_files(ftp_dir, local_dir)

# End of time log
end_time = datetime.now()
diff_time = end_time - started_time
end_time_log = "All operations completed in " + str(diff_time.seconds) + 's\n'
log_file(end_time_log, local_dir)  # write end time stamp

# Log file splitter
msg = "****************************************************************\n"
log_file(msg)  # log event

