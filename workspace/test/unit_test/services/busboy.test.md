import fileUpload from '../../../src/services/busboyUploader'

export const busboyTest = () => {
  describe('test busboyTest functions', () => {
    test('check type file is correct', () => {
      const serviceFile = { 'name': 'files', 'type': 'image', 'howGet': 'file', 'required': true },
        busboy =  {
          objectMode: false,
          highWaterMark: 16384,
          finalCalled: false,
          needDrain: false,
          ending: false,
          ended: false,
          finished: false,
          destroyed: false,
          decodeStrings: true,
          defaultEncoding: 'utf8',
          length: 4257,
          writing: true,
          corked: 0,
          sync: true,
          bufferProcessing: false,
          onwrite: [],
          writecb: [],
          writelen: 4257,
          afterWriteTickInfo: null,
          buffered: [],
          bufferedIndex: 0,
          allBuffers: true,
          allNoop: true,
          pendingcb: 1,
          constructed: true,
          prefinished: false,
          errorEmitted: false,
          emitClose: true,
          autoDestroy: true,
          errored: null,
          closed: false,
          closeEmitted: false,
          [Symbol()]: []
        },
        extension =  '.jpeg'
      const isValid = fileUpload.checkValidExtension({ serviceFile, busboy, extension })

      expect(typeof isValid).toBe('object')
      expect(isValid.error).toEqual(false)
    })
  })
}
