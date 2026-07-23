# Platform Notes

Use this reference only when the local machine path or print UI differs from the current device.

## Path handling

Treat the Amazon label project as a configurable local folder instead of a fixed absolute path.

Derived paths:

- labels file: `<project root>/labels.csv`
- downloads folder: `<project root>/downloads`

Common examples:

- macOS: `/Users/<user>/Documents/amazon-label-printer`
- macOS: `/Users/<user>/Documents/amazon-label-printer-original`
- Windows: `C:\\Users\\<user>\\Documents\\amazon-label-printer`
- Windows: `C:\\Users\\<user>\\Documents\\amazon-label-printer-original`

## macOS printing

- Computer Use may print through Preview or another default PDF viewer.
- Set printer to `Brother MFC-L2690DW`.
- Use `100%` scale when the dialog shows a scale field.
- If the dialog offers `Scale to Fit`, keep it off.

## Windows printing

- Computer Use may print through Edge, Adobe Acrobat, or another default PDF viewer.
- Set printer to `Brother MFC-L2690DW`.
- Prefer `Actual Size` or `100%`.
- Disable `Fit`, `Shrink oversized pages`, or any auto-scaling option.

## Failure handling

- If the project root cannot be found, ask the user for the local project path for that device.
- If the printer name differs on that device, ask the user for the exact installed printer name before printing.
- If the viewer does not expose a reliable `100%` or `Actual Size` control, stop and report the blocker instead of guessing.
