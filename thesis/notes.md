# Notes

- external camera (Sony Edge Webcam not on Linux, alternative describing)

## Questions

- Assistant Supervisor: all three?
- EventStream equivalent for HLS?

## Code Additions / Changes

- NEW: c2patool CLI Subcommand `live` with flags
  - `bind`: address for the server to listen on (receive FFmpeg output)
  - `target`: CDN URL to output the signed fragments
  - `window_size`: number of fragments per Merkle Tree
- NEW: signing function for live
  - `sign_live_bmff`: in line with `sign_fragmented_files` with one additional argument
  - `window_size` configure the number of fragment per sub-Merkle Tree
- CHANGE: Merkle Tree generation (when `window_size` != 0)
  - try to read BMFF Hashes from the potentially already existing output init file (from a previous signing), otherwise init BMFF Hashes
  - group fragments into `window_size` chunks
  - all groups remain unchanged (except the final one)
    - their Merkle Tree stay untouched
  - the last group is the incomplete one that gets changed with every new fragment
    - already signed fragments in this group won't be copied to the destination
    - a placeholder uuid box is only inserted into the new fragment
    - replacing placeholder uuid box expected them to be the same length, now it can replace mismatching length boxes
    - Merkle Tree initialization now supports multiple Tree
      - inserting new Tree
      - updating existing Trees
      - only generate InitHash once then set it on all Merkle Trees
- CHANGE: number of proofs are now always in line to have the comparison hash in the init to be the root of the tree

## TODOs

## FIXMEs
