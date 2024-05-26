import policies from '../../../src/common/middlewares/policy'

export const policyTest = () => {
  describe('test policy functions', () => {
    const extension = ['.jpg', '.jpeg', '.png', '.bmp'],
      files = [
        {
          originalname: 'thumb-raw-05e710ca-9c1d-48d4-94c0-22ac02294908.png',
          path: '/home/ali/Desktop/gateway/uploads/gateway-file-81c7b752-a387-47cf-b874-3236ed1bb114.png',
          encoding: '7bit',
          mimeType: 'image/png',
          newName: 'gateway-file-81c7b752-a387-47cf-b874-3236ed1bb114.png',
          oldName: 'files',
          size: 324945,
        },
      ],
      policy = { size: 12000000 }

    test('check file size and duration', async () => {

      const check = await policies.checkPolicy({ extension, files, policy })

      expect(typeof check).toBe('boolean') 
      expect(check).toEqual(true) 
    })
  })
}
