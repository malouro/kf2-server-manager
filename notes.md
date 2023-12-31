> These are personal notes I had for my own EC2 instance setup for running the server...

# Common Issues

## EC2 Instance Hanging?

Try a simple reboot. `Stop Instance` + `Start Instance` will most likely change the public IP, which can be annoying, so it's preferable to use the `Reboot Instance` option instead.

## Volume Not Mounted?

This is a quick video tutorial I followed to set things up:
> @ref https://www.youtube.com/watch?v=VnO3Lz7Qr0U

To debug, check the following:

- **Volume is attached to the EC2 instance.**
- **Devices exists on the machine.**
  - Check with `df -h`, and if not there, double-check `lsblk`.
    - If `sudo file -s /dev/<device>` yields a positive result, we're good!
    - Otherwise, `mkfs -t xfs /dev/<device>` to set it up. (usually used on a freshly spawned volume)
- **Systems checks passes.** (see `steam-systems-check` -- which checks for the following:)
  - Device exists, which confirms (1)
  - Volume is formatted for the expected device name
  - Volume is mounted at an expected location (ie: `/mnt/<volume-name>` directory)
  - The `~/Steam` path is sym-linked to this mounted location above.

**NOTE:** Running the server will run the systems check for you. (or it should, anyway...)
