# **BLIP Animated Image Format v1.0**

### **File Extension:**

`.blip`

### **MIME Type:**

`image/blip`

---

## **1. Purpose**

The `.blip` format provides a modern, efficient, unambiguous format for short, looping, muted, animated images. Blip files are designed to replace animated GIFs in contexts where inline, silent, looping playback is required with much better compression and even performance.

---

## **2. General Properties**

* **Looping:** Blip files always loop infinitely.
* **Autoplay:** Playback begins automatically.
* **Muted:** No audio is permitted or possible.

* **Compression:** Video frames are stored as a modern video stream (e.g., AV1).
* **Transparency:** Supported via flag (optional for implementers).
* **No metadata required for playback.** All behavior is defined by the spec.

---

## **3. File Structure**

A `.blip` file consists of three sections:

1. **Header (13 bytes)**
2. **Video Payload (variable length)**
3. **CRC32 Checksum (4 bytes)**

### **3.1 Header Format**

| Offset | Size | Field       | Type      | Description                   |
| ------ | ---- | ----------- | --------- | ----------------------------- |
| 0      | 4    | Magic       | uint32    | ASCII "BLIP" (0x42 4C 49 50)  |
| 4      | 1    | Version     | uint8     | Format version (always 1)     |
| 5      | 2    | Width       | uint16 LE | Width in pixels               |
| 7      | 2    | Height      | uint16 LE | Height in pixels              |
| 9      | 2    | Frame Count | uint16 LE | Number of frames in animation |
| 11     | 1    | Framerate   | uint8     | Frames per second (e.g., 30)  |
| 12     | 1    | Flags       | uint8     | Bitfield (see below)          |

#### **Flags Bitfield (1 byte)**

* **Bit 0 (0x01):** Transparency present (if set, frames support alpha)
* **Bits 1-7:** Reserved for future use (must be zero)

---

### **3.2 Video Payload**

* Begins immediately after the 13-byte header.
* Contains AV1-encoded (or other specified codec) video stream, with no audio track.
* If transparency flag is set, alpha data is interleaved or appended as defined in the extended spec.
* Payload size is not explicitly stored; reader must read to 4 bytes before EOF for CRC.

---

### **3.3 CRC32 Checksum**

* Last 4 bytes of the file.
* Calculated over all previous bytes (header + payload).
* Standard CRC32 (ISO 3309/ANSI X3.66).

---

## **4. Playback Semantics**

* **Playback must start immediately when rendered.**
* **Animation must loop infinitely with no manual controls.**
* **No audio must be played or present.**
* **Duration, framerate, and frame count must be respected.**
* **If transparency is not present, alpha is assumed opaque.**

---

## **5. Implementation Notes**

* **Minimum decoder must validate "BLIP" magic number.**
* **If version is unsupported, playback must fail gracefully.**
* **Unknown flag bits must be ignored (future-proofing).**
* **If file is truncated or CRC fails, playback must not proceed.**
* **Alpha support is optional for simple decoders, but required for "full" conformance.**

---

## **6. Example Header (in hex)**

```
42 4C 49 50 01 80 00 40 00 05 00 18 00
```

* `"BLIP"` (magic)
* Version: `01`
* Width: `128`
* Height: `64`
* Frames: `5`
* FPS: `24`
* Flags: `0` (no transparency)

---

## **7. MIME Type Registration**

MIME type for HTTP and file association:

```
Type: image/blip
Extension: .blip
```

---

## **8. Reserved / Future Extensions**

* Future versions may define alternate codecs (e.g., VP9).
* Additional flags for things like frame delays, but not user controls or audio.
* Color space info could be added in future versions for HDR support.

---
