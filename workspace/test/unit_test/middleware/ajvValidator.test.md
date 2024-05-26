import ajv from '../../../src/common/middlewares/ajvValidator'

export const ajvTest = () => {
  describe('test ajv functions', () => {
    const aiServiceFiles = [
      {
        name: 'base64',
        type: 'image',
        howGet: 'base64',
        required: true,
      },
    ]

    test('remove base64 from req body', () => {
      const removed = ajv.removeFileFromInput({
        inputs: {
          base64: 'asda5645614t56h4d5fgsdasd',
          link: 'j2h3b4h2kj34hkj2h34',
        },
        aiServiceFiles,
      })

      expect(typeof removed).toBe('object')
      expect(removed.link).toEqual('j2h3b4h2kj34hkj2h34')
    })

    test('remove base64 and link from req body', () => {
      aiServiceFiles.push({
        name: 'link',
        type: 'image',
        howGet: 'link',
        required: true,
      })
      const removed = ajv.removeFileFromInput({
        inputs: {
          base64: 'asda5645614t56h4d5fgsdasd',
          link: 'j2h3b4h2kj34hkj2h34',
        },
        aiServiceFiles,
      })

      expect(typeof removed).toBe('object')
      expect(removed).toEqual({})
    })
  })
}
